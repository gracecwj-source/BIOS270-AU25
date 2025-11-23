**Write up 4**

1. I will make each sample’s pipeline ends with a salmon job that writes its counts into that sample’s folder. Then, I will submit one extra job for DESeq2 at the end of the script.
After the for loop finishes, submit a DESeq2 with a dependency on all the Salmon jobs (e.g. dependency=afterok:<all-salmon-job-ids>).

2.
```

include { FASTQC   } from './modules/qc/fastqc.nf'
include { TRIMGALORE } from './modules/qc/trimgalore.nf'
include { SALMON   } from './modules/pseudoalign/salmon.nf'
include { DESEQ2   } from './modules/diffexp/deseq2.nf'

process SALMON_INDEX {

    tag "salmon_index"

    input:
    // Reference transcriptome FASTA file
    path transcriptome

    output:
    // Directory containing the built Salmon index
    path "salmon_index" dir true

    """
    salmon index \
        -t ${transcriptome} \
        -i salmon_index
    """
}

def samplesheet_ch = Channel
    .fromPath(params.samplesheet)
    .ifEmpty { error "Missing --samplesheet file: ${params.samplesheet}" }

samples_ch = samplesheet_ch.splitCsv(header: true).map { row ->
    tuple(
        row.sample.trim(),
        file(row.read1.trim(),  absolute: true),
        file(row.read2.trim(),  absolute: true),
        row.condition.trim()
    )
}

workflow {

    FASTQC(samples_ch)
    trimmed_ch = TRIMGALORE(samples_ch)
    def index_ch

    if ( params.index ) {
        log.info "Using existing Salmon index: ${params.index}"
        index_ch = Channel.value( file(params.index) )

    } else if ( params.transcriptome ) {
        log.info "No index provided. Building Salmon index from transcriptome: ${params.transcriptome}"
        def transcriptome_ch = Channel.value( file(params.transcriptome) )
        index_ch = SALMON_INDEX( transcriptome_ch )

    } else {
        error "You must provide either 'index' (existing Salmon index dir) or 'transcriptome' (FASTA) in params.yaml"
    }

    quant_ch = SALMON( trimmed_ch, index_ch )

    if ( params.run_deseq ) {
        // Collect all Salmon outputs into a CSV mapping sample → quant_path
        def quant_paths_ch = quant_ch
            .map { sample, quant, cond -> "${sample},${quant}" }
            .collectFile(
                name: "quant_paths.csv",
                newLine: true,
                seed: "sample,quant_path"    // header line
            )

        DESEQ2( quant_paths_ch, samplesheet_ch )
    }

}

workflow.onComplete {
    log.info "Pipeline finished. Results in: ${params.outdir}"
}

```

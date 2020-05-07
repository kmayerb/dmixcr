# docker-mixcr (docker pull quay.io/kmayerb/dmixcr:0.0.1)

This docker image is based directly on https://github.com/milaboratory/mixcr-docker/tree/master/imgt

The only modification is the removal of an entry point and addition of ps functionality, such that the image can be use as a container within a nextflow script being executed on AWS batch. 

## Example

### Run Script

```bash
INSERT
```

### Config

```bash
INSERT
```

### Workflow

```groovy
params.output_folder = "." 
params.batchfile = "batchfile.csv"

Channel.from(file(params.batchfile))
        .splitCsv(header: true, sep: ",")
        .map { sample ->
        [sample.name, file(sample.fastq1)]}
        .set{ fastq_ch }

process align {
    
    \\replace container 'milaboratory/mixcr:3-imgt' with container `quay.io/kmayerb/dmixcr:0.0.1`
    
    container `quay.io/kmayerb/dmixcr:0.0.1`
    
    publishDir params.output_folder

    input:
    set name, val(fastq1) from fastq_ch

    output:  
    file "${name}.result.txt" into align_result_channel

    script:
    """
    mixcr align ${fastq1} test2.vdjca --species hsa
    mixcr assemble test2.vdjca output.clns
    mixcr exportClones output.clns output.clns.txt
    mixcr exportAlignments test2.vdjca > ${name}.result.txt
    """
}
```






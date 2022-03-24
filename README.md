# htslib.cr

[![CI](https://github.com/bio-crystal/htslib.cr/actions/workflows/ci.yml/badge.svg)](https://github.com/bio-crystal/htslib.cr/actions/workflows/ci.yml)
[![Slack](http://img.shields.io/badge/slack-bio--crystal-purple?labelColor=000000&logo=slack)](https://bio-crystal.slack.com/)
[![Get invite to BioCrystal](http://img.shields.io/badge/Get_invite_to_BioCrystal-purple?labelColor=000000&logo=slack)](https://join.slack.com/t/bio-crystal/shared_invite/zt-tas46pww-JSEloonmn3Ma5eD2~VeT_g)

htslib.cr is the [Crystal](https://github.com/crystal-lang/crystal) bindings to [HTSlib](https://github.com/samtools/htslib), a C library for for high-throughput sequencing data formats. It allows you to read and write file formats commonly used in genomics, such as [SAM, BAM, VCF, and BCF](http://samtools.github.io/hts-specs/) in the Crystal language.

:rocket: Feel free to fork it out! 

:hatching_chick: alpha stage.

## Requirements

* [Crystal](https://crystal-lang.org)
* [HTSlib](https://github.com/samtools/htslib)
  * Ubuntu : `apt install libhts-dev`
  * macOS  : `brew install htslib`
  * Any OS : Build from [source code](https://github.com/samtools/htslib)
  * Make sure that `pkg-config` can detect htslib: `pkg-config --libs htslib`

## Installation

Add htslib to your `shard.yml`:

   ```yaml
   dependencies:
     htslib:
       github: bio-crystal/htslib.cr
   ```

Run `shards install`

## Usage

SAM / BAM / CRAM

```crystal
require "htslib/hts/bam"

bam = HTS::Bam.open(bam_path)

bam.each do |r|
  p name:  r.qname,
    flag:  r.flag.value,
    pos:   r.start + 1,
    mpos:  r.mate_start + 1,
    mqual: r.mapping_quality,
    seq:   r.sequence,
    cigar: r.cigar,
    qual:  r.base_qualities.map { |i| (i + 33).chr }.join
  # tag:   r.tag("MC")
end

bam.close
```

VCF / BCF

```crystal
require "htslib/hts/bcf"

bcf = HTS::Bcf.open(bcf_path)

bcf.each do |r|
  p chrom:  r.chrom,
    start:  r.start,
    stop:   r.stop,
    id:     r.id,
    qual:   r.qual,
    filter: r.filter,
    ref:    r.ref,
    alt:    r.alt,
  # alleles r.alleles
  # info:   r.info,
  # format  r.format
end

bcf.close
```

## API Overview

* High level API - Create as needed. But don't overdo it.
* Low level API - Native C bindings to HTSLib generated by [crystal_lib](https://github.com/crystal-lang/crystal_lib).
* See [API documentation](https://bio-crystal.github.io/htslib.cr/).

```
 ┌──────────────────── HTS ────────────────────┐
 │                                             │
 │ ┌─ Bam ────────┬─ Bcf ───────┬─ Tabix ────┐ │
 │ │ SAM BAM CRAM │ VCF BCF     │ TABIX      │ │
 │ └──────────────┴─────────────┴────────────┘ │
 │                     ┌─LibHTS2───────────┐   │
 │ ┌─LibHTS────────────┤ Macro functions   ├─┐ │
 │ │ Native C bindings └───────────────────┘ │ │
 │ └─────────────────────────────────────────┘ │
 └─────────────────────────────────────────────┘
```

## Related Work

* [ruby-htslib](https://github.com/kojix2/ruby-htslib)

## Contributing

htslib.cr is an immature, work-in-progress library, and pull requests such as small typo fixes are welcome.

    Do you need commit rights to htslib.cr?
    Do you want to get admin rights and take over the project?
    If so, please feel free to contact us @kojix2.

## Benchmark

https://github.com/brentp/vcf-bench

code: https://github.com/kojix2/vcf-bench/blob/kojix2/crystal-htslib/read.cr

![image](https://user-images.githubusercontent.com/5798442/155878129-92f25b51-4b17-4b30-8b77-616ada93c0b4.png)

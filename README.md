# HTS.cr

[![CI](https://github.com/bio-cr/hts.cr/actions/workflows/ci.yml/badge.svg)](https://github.com/bio-cr/hts.cr/actions/workflows/ci.yml)
[![Slack](http://img.shields.io/badge/slack-bio--crystal-purple?labelColor=000000&logo=slack)](https://bio-crystal.slack.com/)
[![Get invite to BioCrystal](http://img.shields.io/badge/Get_invite_to_BioCrystal-purple?labelColor=000000&logo=slack)](https://join.slack.com/t/bio-crystal/shared_invite/zt-tas46pww-JSEloonmn3Ma5eD2~VeT_g)
[![DOI](https://zenodo.org/badge/351622305.svg)](https://zenodo.org/badge/latestdoi/351622305)

HTS.cr provides [Crystal](https://github.com/crystal-lang/crystal) bindings for [HTSlib](https://github.com/samtools/htslib) that allows you to read and write file formats commonly used in genomics, such as [SAM, BAM, VCF, and BCF](http://samtools.github.io/hts-specs/).

:information_source: Method names will be changed to rust-htslib style in the next version.

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
       github: bio-cr/hts.cr
       branch: develop
   ```

Run `shards install`

## Usage

Read SAM / BAM / CRAM

```crystal
require "hts/bam"

bam = HTS::Bam.open(bam_path)

bam.each do |r|
  p name: r.qname,
    flag: r.flag.value,
    chrm: r.chrom,
    strt: r.pos + 1,
    mapq: r.mapq,
    cigr: r.cigar.to_s,
    mchr: r.mate_chrom,
    mpos: r.mpos + 1,
    isiz: r.isize,
    seqs: r.seq,
    qual: r.qual.map { |i| (i + 33).chr }.join,
    axMC: r.aux("MC")
end

bam.close
```

Read VCF / BCF

```crystal
require "hts/bcf"

bcf = HTS::Bcf.open(bcf_path)

bcf.each do |r|
  p chrom:  r.chrom,
    pos:    r.pos,
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

* High level API - Classes include Bam, Bcf, Tabix, Faidx, etc.
* LibHTS - Native C bindings to HTSLib generated by [crystal_lib](https://github.com/crystal-lang/crystal_lib).
* For more information, please see [API documentation](https://bio-cr.github.io/hts/).

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

## Looking for flexibility?

The Crystal language is suited for creating efficient command-line tools. The Ruby language, on the other hand, is suited for exploratory analysis.

* [ruby-htslib](https://github.com/kojix2/ruby-htslib)

## Contributing

:rocket: Feel free to fork it out! 

    git clone https://github.com/bio-cr/hts.cr
    cd hts.cr
    crystal run test/run_all.cr


HTS.cr is an immature, work-in-progress library, and pull requests such as small typo fixes are welcome.

    Do you need commit rights to hts?
    Do you want to get admin rights and take over the project?
    Please feel free to contact us @kojix2.

## Benchmark

https://github.com/brentp/vcf-bench

code: https://github.com/kojix2/vcf-bench/blob/kojix2/crystal-htslib/read.cr


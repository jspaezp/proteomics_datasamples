

# mzML files

These four files are a lossy-compressed version of the original files
in the pride repo **PXD015087**

```bash
#!/bin/bash
files="20181210_15cm_3_HelaDilution_30min_0ng_28Hz_R1.raw  20181210_15cm_3_HelaDilution_30min_0ng_41Hz_R1.raw   20181210_15cm_3_HelaDilution_30min_1000ng_28Hz_R1.raw  20181210_15cm_3_HelaDilution_30min_1000ng_41Hz_R1.raw"

for curr_file in $files ; do

    curl ftp://ftp.pride.ebi.ac.uk/pride/data/archive/2019/12/PXD015087/${curr_file} --output ${curr_file}
    docker run --rm -v $PWD/data:/data chambm/pwiz-skyline-i-agree-to-the-vendor-licenses \
        wine msconvert \
        /data/${curr_file} \
        -o /data  \
        --filter "peakPicking true 1-" \
        --zlib \
        --32 \
        --filter "scanTime [600,630]" \
        --filter "zeroSamples removeExtra" \
        --filter "threshold count 2000 most-intense"
    # Extracts from min 10 to 10.5
done
```

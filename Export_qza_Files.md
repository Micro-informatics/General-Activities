# Make a Bash Script for extracting files from differential.qza into csv 

> **Create an sh. file to save the script**
````
nano make_lfc_tables.sh
````
> ***copy and paste following script***
````
#!/bin/bash

# Base output folder
OUTPUT_BASE="exported_L3"

# Loop through each differentials_*_A folder
for dir in differentials_*_A; do
  if [ -d "$dir" ]; then
    echo "🔄 Processing $dir"

    # Extract treatment name without the leading 'differentials_' and trailing '_A'
    treatment=$(echo "$dir" | sed -E 's/^differentials_//' | sed -E 's/_A$//')

    # Define output path
    output_path="${OUTPUT_BASE}/${treatment}"
    mkdir -p "$output_path"

    # Export the .qza file contents
    qiime tools export \
      --input-path "${dir}/differentials.qza" \
      --output-path "$output_path"

    echo "✅ Exported to: $output_path"
  fi
done
````
> Press ctrl+o , Enter , ctrl+X

> ***Then make it executable by running following command*** 

````
chmod +x export_all_differentials.sh
````
> Execute it
````
./export_all_differentials.sh
````
> ***Output files will be in exported_L3 director with as following*** 
````
exported_L3/
├── SOBI_C_L/
│   ├── lfc_slice.csv
│   ├── p_val_slice.csv
│   ├── ...
├── NG_Bio_U/
│   ├── ...
...
````

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
# Make nano make_lfc_tables.sh to cobine output files into LFC_TSV

````
nano make_lfc_tables.sh
````
````
#!/bin/bash

BASE_DIR="exported_L3"

# Loop through each treatment folder
for dir in "$BASE_DIR"/*/; do
  treatment=$(basename "$dir")
  echo "🔧 Processing: $treatment"

  # Input files
  lfc="${dir}lfc_slice.csv"
  pval="${dir}p_val_slice.csv"
  qval="${dir}q_val_slice.csv"
  se="${dir}se_slice.csv"
  w="${dir}w_slice.csv"

  # Check if files exist
  if [[ ! -f "$lfc" || ! -f "$pval" || ! -f "$qval" || ! -f "$se" || ! -f "$w" ]]; then
    echo "⚠️  Skipping $treatment — missing one or more input files"
    continue
  fi

  # Output file with treatment name prefix
  output_file="${dir}${treatment}_LFC.tsv"

  # Merge slices into one table
  paste \
    <(tail -n +2 "$lfc" | cut -d',' -f1) \
    <(tail -n +2 "$lfc" | cut -d',' -f2) \
    <(tail -n +2 "$pval" | cut -d',' -f2) \
    <(tail -n +2 "$qval" | cut -d',' -f2) \
    <(tail -n +2 "$se"   | cut -d',' -f2) \
    <(tail -n +2 "$w"    | cut -d',' -f2) |
  sed '1iFeatureID\tLFC\tp_value\tq_value\tSE\tW' > "$output_file"

  echo "✅ Created: $output_file"
done

````
> **ctrl+o , Enter , ctrl+X***
````
chmod +x make_lfc_tables.sh
./make_lfc_tables.sh
````

**COllect All the TSV files from the directories into one Folder TSV (** 
````
mkdir -p LFC && find exported_L3 -type f -name "*_LFC.tsv" -exec cp {} LFC/ \;
````

**In VS convert all TSV into Excel**
> /scratch/user/name/Project/ITS_Full/ITS_trimmed/exported_L3/SOBI_TBA_U
> exported_L3 contains all directories for the treatments with thier respective LFC files 

````
import os
import pandas as pd

# Use current directory
input_dir = "."
output_dir = "ITS_LFC_2023_excel"
os.makedirs(output_dir, exist_ok=True)

# Loop through all TSVs in the current directory
for file in os.listdir(input_dir):
    if file.endswith("_LFC.tsv"):
        df = pd.read_csv(file, sep="\t")
        output_file = os.path.join(output_dir, file.replace(".tsv", ".xlsx"))
        df.to_excel(output_file, index=False)
        print(f"✅ Converted: {file} → {output_file}")
````



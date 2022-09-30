
# Spectral library generated with BiblioSpec

it is internally an sqlite3 file.
I also included the `.ssl` file that was used to generate such library.


Basically used this function to convert mokapot psms.txt outputs to it.

```python
import pandas as pd

def convert_to_ssl(input_file, output_file):
    SPEC_ID_REGEX = re.compile(r"^(.*)_(\d+)_(\d+)_(\d+)$")

    def _parse_line(line):
        outs = SPEC_ID_REGEX.match(line.SpecId).groups()
        file_name, spec_number, charge, _ = outs

        first_period = 0
        last_period = 0
        for i, x in enumerate(line.Peptide):
            if x == ".":
                if first_period == 0:
                    first_period = i
                else:
                    last_period = i

        sequence = line.Peptide[first_period + 1 : last_period]

        line_out = {
            "file": file_name,
            "scan": spec_number,
            "charge": charge,
            "sequence": sequence,
            "score-type": "PERCOLATOR QVALUE",
            "score": line["mokapot q-value"],
        }
        return line_out

    df = pd.read_csv(
        input_file,
        sep="\t",
        dtype={
            "SpecId": str,
            "Label": bool,
            "ScanNr": np.int32,
            "ExpMass": np.float16,
            "CalcMass": np.float16,
            "Peptide": str,
            "mokapot score": np.float16,
            "mokapot q-value": np.float32,
            "mokapot PEP": np.float32,
            "Proteins": str,
        },
    )

    out_df = pd.DataFrame([_parse_line(x) for _, x in df.iterrows()])
    out_df.to_csv(output_file, sep="\t", index=False, header=True)
```

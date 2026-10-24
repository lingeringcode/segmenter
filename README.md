# Segmenter

[![DOI](https://zenodo.org/badge/1432300318.svg)](https://doi.org/10.5281/zenodo.23620926)

**What is it?**: A Python commandline tool developed specifically for qualitative researchers, who need to quickly segment text files either in `.txt` or `.docx` file formats.

**What file formats can I use?**: Segmenter works with pre-existing folder of `.txt` or `.docx` files. It also works well with a pre-existing MAXQDA document system of files, which you can easily export with MAXQDA's `Archive Data` feature.

**Include linguistic CoNLLU-U files**: Segmenter converts all input files into CoNLL-U text files to reliably complete this work, so you can also optionally include these outputs in the output folder with segmented documents.

**How to cite this project**: Please cite this project, if you used it in your research process. Thank you!

- APA format: Lindgren, Chris A. (2026). lingeringcode/segmenter: Segmenter (Version 1.0.0) [Computer software]. Zenodo. https://doi.org/10.5281/ZENODO.23620926

Read more about how Segmenter works below.

## 1. Installation and Requirements

- Developed and tested on Python 3.12.4
- See `requirements.txt` for Python code modules.
- The input folder with the .docx files need to be inside of the segmenter project folder. Your directory can includes a nested structure.

### 1.1 Set up a Python environment with `.venv`

Using a project-local virtual environment is the recommended way to install and run Segmenter's Python dependencies. Python includes the `venv` module, so you do not need to download `.venv` itself.

The commands below create a `.venv` directory in the project folder, activate it, and install the project requirements.

Use Python 3.12, the version this project was developed and tested with.

For additional help, see the official [Python `venv` documentation](https://docs.python.org/3/library/venv.html).

#### macOS

```bash
python3.12 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

#### Linux

```bash
python3.12 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

If the `venv` module is unavailable, install the appropriate Python 3.12 virtual environment package for your Linux distribution, then repeat the commands above.

#### Windows (PowerShell)

```powershell
py -3.12 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

In Command Prompt, activate the environment with `.venv\Scripts\activate.bat` instead. If PowerShell prevents script activation, follow Microsoft's guidance for the execution-policy setting appropriate to your computer.

After setup, activate `.venv` in each new terminal before running the segmenter or installing its spaCy model. To leave the environment, run `deactivate`. If you use an editor such as VS Code, select the Python interpreter located inside the project's `.venv` directory.


### 1.2 Install a spaCy language model

The segmenter uses spaCy's `en_core_web_sm` model by default. Install it into the same Python environment used to run the script:

```bash
python -m spacy download en_core_web_sm
```

To use a different language or model size, install the desired model and pass its name with `--model`. For example:

```bash
python -m spacy download es_core_news_md
python segmenter.py --input_folder Documents --input_format docx --unit all --model es_core_news_md
```

Run the download command after activating your virtual environment, if using one, so the model is available to the segmenter.

## 2. Add Documents to the segmenter project folder

### Option 1 - Existing Files

If you already have your documents saved as either `.txt` or `.docx` files, then copy your existing folder into this `segmenter` project folder. (See the example case: `Example_Documents`.)

### Option 2 - Files in MAXQDA's Document System

If you have already imported your documents into MAXQDA, you can export your Document System by:

1. Navigate to the **Home** tab view and select **Archive Data**.
2. In the new dialog window, deselect all radio buttons, if needed, and press **OK**.
3. When prompted, save the archived data in the segmenter folder.
4. Move the **Documents** folder that will be within the "**Archive_...**" folder into the root of this **segmenter** folder. The resulting structure will look the following:
    ```
    segmenter
    |_/Documents
    |_README.md
    |_requirements.txt
    |_segmenter.py
    ```

## 3. Run Segmenter

### How to Use Segmenter

```
usage: python segmenter.py [-h] --input_folder INPUT_FOLDER --input_format {docx,txt}
                           [--output_format {docx,txt}] --unit {s,t,c,all}
                           [--include_conllu] [--model MODEL]

options:
  -h, --help            show this help message and exit
  --input_folder INPUT_FOLDER
                        Path to folder containing input files (searched recursively).
  --input_format {docx,txt}
                        Format of files in the input folder.
  --output_format {docx,txt}
                        Format for segmented output files. Defaults to docx.
  --include_conllu      Also copy CoNLL-U text files into Segmented_Documents/conllu-txts.
  --unit {s,t,c,all}    `s`==sentences; `t`==t-units; `c`==clauses; `all`==all three.
  --model MODEL         Spacy model to use (e.g. 'es_core_news_md'). Default=='en_core_web_sm'
```

#### Example Cases

**Case 1: docx to docx**: To segment every `.docx` file under `Documents` into sentences and save the segments as Word documents, run the following command:

```
python segmenter.py --input_folder Documents --input_format docx --unit s
```

`--input_format` selects the input file type (`docx` or `txt`), and
`--output_format` selects the segmented file type (`docx` or `txt`). Output
defaults to `.docx`.

**Case 2: txt to txt**:  To segment text inputs and save plain-text segments:

```
python segmenter.py --input_folder Documents --input_format txt --output_format txt --unit all
```

**Case 3: Include CoNNLL-U files**: Add `--include_conllu` to include copies of the generated CoNLL-U files in the segmented output directory. They are saved separately under `Segmented_Documents/conllu-txts/`, with the input folder hierarchy preserved:

```
python segmenter.py --input_folder Documents --input_format docx --output_format txt --unit all --include_conllu
```

Segmenter preserves the input folder hierarchy under `Segmented_Documents/<unit>/`. For example, `Documents/Thread_1/100.docx` produces `Segmented_Documents/sentences/Thread_1/100.docx` when output is DOCX, or `.../100.txt` when output is TXT. Each output file contains one segment per paragraph (DOCX) or line (TXT). The `txts/` and `conllu-txts/` intermediate directories are removed from the input folder after segmentation completes.

When `--include_conllu` is set, CoNLL-U files are copied to `Segmented_Documents/conllu-txts/` before the input-folder intermediates are removed. If processing or copying fails, cleanup is not reached, so the intermediate files remain available for troubleshooting.

Use `--unit all` to generate sentences, T-units, and clauses. The three outputs are saved under `Segmented_Documents/sentences/`, `Segmented_Documents/t_units/`, and `Segmented_Documents/clauses/`, each preserving the source folder hierarchy.

## How Segmenter Works

`segmenter.py` CLI runs a three-stage procedure over your provided input directory:

1. **Load text:** For DOCX inputs, recursively extracts document paragraphs to temporary UTF-8 `.txt` files under `Documents/txts/`. For TXT inputs, reads the source files directly. Relative paths are preserved; Word lock files are ignored.
2. **Annotate text:** Loads the selected spaCy model, processes each text line, and serializes sentences and token-level annotations in temporary CoNLL-U files under `Documents/conllu-txts/`.
3. **Segment and write output:** Parses the CoNLL-U files, selects the requested unit(s), and writes one segment per paragraph or line in the selected output format, preserving paths under `Segmented_Documents/`.
4. **Copy and clean up:** If `--include_conllu` is selected, copies the CoNLL-U files under `Segmented_Documents/conllu-txts/`; after successful output generation, removes the `txts/` and `conllu-txts/` intermediate directories from the input folder.

### CLI options

- `--input_folder` is the root of the source file tree. Discovery is recursive.
- `--input_format` is required and identifies the source files as `docx` or `txt`.
- `--output_format` selects `docx` or `txt` for segmented outputs; it defaults to `docx`.
- `--include_conllu` optionally copies generated CoNLL-U files into a separate `Segmented_Documents/conllu-txts/` subfolder.
- `--unit` is required and accepts `s` (sentences), `t` (T-units), `c` (clauses), or `all` (all three, each saved to its own output directory).
- `--model` optionally selects an installed spaCy model. It defaults to `en_core_web_sm`.

### Python function (`def`) reference

- `convert_docx_to_txt(input_folder, output_folder)` recursively reads Word documents, extracts their paragraphs, and writes matching UTF-8 text files.
- `copy_conllu_files(input_folder, output_folder)` copies CoNLL-U text files to a destination while preserving their relative directory structure.
- `sentences_to_conllu(doc, sent_id, prefix="")` prints a spaCy document's sentence headers, sentence text, and token annotations in CoNLL-U format; it returns the next sentence ID.
- `main(input_folder, out_dir_txt, conllu_output_folder, unit_type, docx_output_folder, model, prefix="")` loads spaCy and orchestrates text extraction followed by CoNLL-U generation.
- `segment_txt_as_docx(input_folder, output_folder, unit_type, output_format="docx")` reads CoNLL-U files, extracts the requested sentence, T-unit, and/or clause segments, and writes DOCX or TXT outputs with their relative paths preserved.

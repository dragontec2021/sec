# EDGAR-CRAWLER: Unlock the Power of Financial Documents 🚀
![EDGAR-CRAWLER-LOGO](images/edgar-crawler-logo-white-bg.jpeg)

Tired of sifting through endless financial reports of 100+ pages, struggling to extract meaningful insights? 

📊 `EDGAR-CRAWLER` is an open-source & optimized toolkit that retrieves key information from financial reports. It can crawl any report found in the [SEC EDGAR](https://www.sec.gov/edgar.shtml) database, the web repository for all publicly traded companies in the USA.

Most importantly, apart from downloading EDGAR filings like other standard toolkits, `EDGAR-CRAWLER` can also preprocess and convert them from lengthy and unstructured documents into clean and easy-to-use JSON files. 

### `EDGAR-CRAWLER` has 2 core modules:
📥🕷️ Business Documents Crawling: Utilize the power of the `edgar_crawler.py` module to effortlessly crawl and download financial reports for every publicly-traded company within your specified years.

🔍📑 Item Extraction: Extract and clean specific text sections such as Risk Factors or Management's Discussion & Analysis from 10-K documents (annual reports), 10-Q documents (quarterly reports) and 8-K documents (current reports) using the `extract_items.py` module. Get straight to the heart of the information that matters most.

## Who Can Benefit from `EDGAR-CRAWLER`?
📚 Academics: Enhance your NLP research in economics & finance or business management by accessing and analyzing financial data efficiently.

💼 Professionals: Strengthen financial analysis, strategic planning, and decision-making with comprehensive, easy-to-interpret financial reports.

🛠 Developers: Seamlessly integrate financial data into your models, applications, and experiments using our open-source toolkit.

### Star History

[![Star History Chart](https://api.star-history.com/svg?repos=nlpaueb/edgar-crawler&type=Date)](https://star-history.com/#nlpaueb/edgar-crawler&Date)

## 🚨 News
- 2024-10-13: We now also support the JSON extraction of 10-Q filings. ([@Bailefan](https://github.com/Bailefan))
- 2024-10-05: We added support for JSON extraction of 8-K filings too. ([@Bailefan](https://github.com/Bailefan))
- 2023-12-06: We had a Lightning Talk about EDGAR-CRAWLER at the 3rd Workshop for Natural Language Processing Open Source Software [(NLP-OSS)](https://nlposs.github.io/2023/), hosted at EMNLP 2023, in Singapore.
- 2023-01-16: EDGAR-CORPUS, the biggest financial NLP corpus (generated from `EDGAR-CRAWLER`), is available as a HuggingFace 🤗 dataset card. See [Accompanying Resources](#Accompanying-Resources) for more details.
- 2022-10-13: Updated documentation and fixed a minor import bug.
- 2022-04-03: `EDGAR-CRAWLER` is available for Windows systems too.
- 2021-11-11: We presented EDGAR-CORPUS, our sister work that started it all, at [ECONLP 2021](https://lt3.ugent.be/econlp/) (EMNLP Workshop) at the Dominican Republic. See [Accompanying Resources](#Accompanying-Resources) for more details.

## Table of Contents
- [Install](#install)
- [Usage](#usage)
- [Example Outputs](#example-outputs)
- [Citation](#citation)
- [Accompanying Resources](#accompanying-resources)
- [Contributing](#contributing)
- [License](#license)

## Install
- Before starting, it's recommended to create a new virtual environment using Python 3.8. We recommend [installing](https://docs.anaconda.com/anaconda/install/index.html) and [using Anaconda](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#creating-an-environment-with-commands) for this.
- Install dependencies via `pip install -r requirements.txt`

## Usage
- Before running any script, you should edit the `config.json` file, which configures the behavior of our 2 modules. 
  - Arguments for `edgar_crawler.py`, the module to download financial reports:
      - `start_year XXXX`: the year range to start from (default is 2021).
      - `end_year YYYY`: the year range to end to (default is 2021).
      - `quarters`: the quarters that you want to download filings from (List).<br> Default value is: `[1, 2, 3, 4]`.
      - `filing_types`: list of filing types to download.<br> Default value is: `['10-K', '8-K']`.
      - `cik_tickers`: list or path of file containing CIKs or Tickers. e.g. `[789019, "1018724", "AAPL", "TWTR"]` <br>
        In case of file, provide each CIK or Ticker in a different line.  <br>
      If this argument is not provided, then the toolkit will download annual reports for all the U.S. publicly traded companies.
      - `user_agent`: the User-agent (name/email) that will be declared to SEC EDGAR.
      - `raw_filings_folder`: the name of the folder where downloaded filings will be stored.<br> Default value is `'RAW_FILINGS'`.
      - `indices_folder`: the name of the folder where EDGAR TSV files will be stored. These are used to locate the annual reports. Default value is `'INDICES'`.
      - `filings_metadata_file`: CSV filename to save metadata from the reports.
      - `skip_present_indices`: Whether to skip already downloaded EDGAR indices or download them nonetheless.<br> Default value is `True`.
  - Arguments for `extract_items.py`, the module to clean and extract textual data from already-downloaded reports:
    - `raw_filings_folder`: the name of the folder where the downloaded documents are stored.<br> Default value s `'RAW_FILINGS'`.
    - `extracted_filings_folder`: the name of the folder where extracted documents will be stored.<br> Default value is `'EXTRACTED_FILINGS'`.<br> For each downloaded report, a corresponding JSON file will be created containing the item sections as key-pair values.
    - `filings_metadata_file`: CSV filename to load reports metadata (Provide the same csv file as in `edgar_crawler.py`).
    - `filing_types`: list of filing types to extract.
    - `include_signature`: Whether to include the signature section after the last item or not.
    - `items_to_extract`: a list with the certain item sections to extract. <br>
      e.g. `['7','8']` to extract 'Management’s Discussion and Analysis' and 'Financial Statements' section items for 10-K reports.<br>
      By default, this list is empty, in which case all items are extracted.
    - `remove_tables`: Whether to remove tables containing mostly numerical (financial) data. This work is mostly to facilitate NLP research where, often, numerical tables are not useful.
    - `skip_extracted_filings`: Whether to skip already extracted filings or extract them nonetheless.<br> Default value is `True`.

- To download financial reports from EDGAR, run `python edgar_crawler.py`.
- To clean and extract specific item sections from already-downloaded documents, run `python extract_items.py`.
  - Reminder: We currently support the extraction of 10-K, 10-Q and 8-K documents.
  - Note: For older 10-Q filings, it might not be possible to extract any items for specific parts. Because of this, we also include each full `part` in the output file as a separate entry.

## Example Outputs
Examples of the outputs you can expect for the different files are shown here:

### 10-K
Original report: [Apple 10-K from 2022](https://www.sec.gov/Archives/edgar/data/320193/000032019322000108/aapl-20220924.htm)
```
{
  "cik": "320193",
  "company": "Apple Inc.",
  "filing_type": "10-K",
  "filing_date": "2022-10-28",
  "period_of_report": "2022-09-24",
  "sic": "3571",
  "state_of_inc": "CA",
  "state_location": "CA",
  "fiscal_year_end": "0924",
  "filing_html_index": "https://www.sec.gov/Archives/edgar/data/320193/0000320193-22-000108-index.html",
  "htm_filing_link": "https://www.sec.gov/Archives/edgar/data/320193/000032019322000108/aapl-20220924.htm",
  "complete_text_filing_link": "https://www.sec.gov/Archives/edgar/data/320193/0000320193-22-000108.txt",
  "filename": "320193_10K_2022_0000320193-22-000108.htm",
  "item_1": "Item 1. Business\nCompany Background\nThe Company designs, manufactures ...",
  "item_1A": "Item 1A. Risk Factors\nThe Company’s business, reputation, results of ...",
  "item_1B": "Item 1B. Unresolved Staff Comments\nNone.",
  "item_1C": "",
  "item_2": "Item 2. Properties\nThe Company’s headquarters are located in Cupertino, California. ...",
  "item_3": "Item 3. Legal Proceedings\nEpic Games\nEpic Games, Inc. (“Epic”) filed a lawsuit ...",
  "item_4": "Item 4. Mine Safety Disclosures\nNot applicable. ...",
  "item_5": "Item 5. Market for Registrant’s Common Equity, Related Stockholder ...",
  "item_6": "Item 6. [Reserved]\nApple Inc. | 2022 Form 10-K | 19",
  "item_7": "Item 7. Management’s Discussion and Analysis of Financial Condition ...",
  "item_8": "Item 8. Financial Statements and Supplementary Data\nAll financial ...",
  "item_9": "Item 9. Changes in and Disagreements with Accountants on Accounting and Financial Disclosure\nNone.",
  "item_9A": "Item 9A. Controls and Procedures\nEvaluation of Disclosure Controls and ...",
  "item_9B": "Item 9B. Other Information\nRule 10b5-1 Trading Plans\nDuring the three months ...",
  "item_9C": "Item 9C. Disclosure Regarding Foreign Jurisdictions that Prevent Inspections\nNot applicable. ...",
  "item_10": "Item 10. Directors, Executive Officers and Corporate Governance\nThe information required ...",
  "item_11": "Item 11. Executive Compensation\nThe information required by this Item will be included ...",
  "item_12": "Item 12. Security Ownership of Certain Beneficial Owners and Management and ...",
  "item_13": "Item 13. Certain Relationships and Related Transactions, and Director Independence ...",
  "item_14": "Item 14. Principal Accountant Fees and Services\nThe information required ...",
  "item_15": "Item 15. Exhibit and Financial Statement Schedules\n(a)Documents filed as part ...",
  "item_16": "Item 16. Form 10-K Summary\nNone.\nApple Inc. | 2022 Form 10-K | 57",
}
```

### 10-Q
Original report: [Apple 10-Q from Q1 2024](https://www.sec.gov/Archives/edgar/data/320193/000032019324000069/aapl-20240330.htm)
```
{
  "cik": "320193",
  "company": "Apple Inc.",
  "filing_type": "10-Q",
  "filing_date": "2024-05-03",
  "period_of_report": "2024-03-30",
  "sic": "3571",
  "state_of_inc": "CA",
  "state_location": "CA",
  "fiscal_year_end": "0928",
  "filing_html_index": "https://www.sec.gov/Archives/edgar/data/320193/0000320193-24-000069-index.html",
  "htm_filing_link": "https://www.sec.gov/Archives/edgar/data/320193/000032019324000069/aapl-20240330.htm",
  "complete_text_filing_link": "https://www.sec.gov/Archives/edgar/data/320193/0000320193-24-000069.txt",
  "filename": "320193_10Q_2024_0000320193-24-000069.htm",
  "part_1": "PART I - FINANCIAL INFORMATION\nItem 1. Financial Statements\nApple Inc.\nCONDENSED CONSOLIDATED STATEMENTS ...",
  "part_1_item_1": "Item 1. Financial Statements\nApple Inc.\nCONDENSED CONSOLIDATED STATEMENTS ...",
  "part_1_item_2": "Item 2. Management’s Discussion and Analysis of Financial Condition and ...",
  "part_1_item_3": "Item 3. Quantitative and Qualitative Disclosures About Market Risk\nThere have ...",
  "part_1_item_4": "Item 4. Controls and Procedures\nEvaluation of Disclosure Controls and ...",
  "part_2": "PART II - OTHER INFORMATION\nItem 1. Legal Proceedings\nDigital Markets Act Investigations\nOn ...",
  "part_2_item_1": "Item 1. Legal Proceedings\nDigital Markets Act Investigations\nOn March 25, 2024, ...",
  "part_2_item_1A": "Item 1A. Risk Factors\nThe Company’s business, reputation, ...",
  "part_2_item_2": "Item 2. Unregistered Sales of Equity Securities and Use of ...",
  "part_2_item_3": "Item 3. Defaults Upon Senior Securities\nNone.",
  "part_2_item_4": "Item 4. Mine Safety Disclosures\nNot applicable.",
  "part_2_item_5": "Item 5. Other Information\nInsider Trading Arrangements\nNone.",
  "part_2_item_6": "Item 6. Exhibits\nIncorporated by Reference\nExhibit\nNumber\nExhibit Description ..."
}
```
**Note:** `part_1` and `part_2` contain the full detected text for that part.

### 8-K
Original report: [Apple 8-K from 2022-08-19](https://www.sec.gov/Archives/edgar/data/320193/000119312522225365/d366128d8k.htm)
```
{
  "cik": "320193",
  "company": "Apple Inc.",
  "filing_type": "8-K",
  "filing_date": "2022-08-19",
  "period_of_report": "2022-08-17",
  "sic": "3571",
  "state_of_inc": "CA",
  "state_location": "CA",
  "fiscal_year_end": "0924",
  "filing_html_index": "https://www.sec.gov/Archives/edgar/data/320193/0001193125-22-225365-index.html",
  "htm_filing_link": "https://www.sec.gov/Archives/edgar/data/320193/000119312522225365/d366128d8k.htm",
  "complete_text_filing_link": "https://www.sec.gov/Archives/edgar/data/320193/0001193125-22-225365.txt",
  "filename": "320193_8K_2022_0001193125-22-225365.htm",
  "item_1.01": "",
  "item_1.02": "",
  "item_1.03": "",
  "item_1.04": "",
  "item_1.05": "",
  "item_2.01": "",
  "item_2.02": "",
  "item_2.03": "",
  "item_2.04": "",
  "item_2.05": "",
  "item_2.06": "",
  "item_3.01": "",
  "item_3.02": "",
  "item_3.03": "",
  "item_4.01": "",
  "item_4.02": "",
  "item_5.01": "",
  "item_5.02": "Item 5.02 Departure of Directors or Certain Officers; Election of Directors; Appointment ...",
  "item_5.03": "Item 5.03 Amendments to Articles of Incorporation or Bylaws; Change in Fiscal Year.\nOn August 17, 2022, Apple’s Board approved and adopted amended and restated bylaws ...",
  "item_5.04": "",
  "item_5.05": "",
  "item_5.06": "",
  "item_5.07": "",
  "item_5.08": "",
  "item_6.01": "",
  "item_6.02": "",
  "item_6.03": "",
  "item_6.04": "",
  "item_6.05": "",
  "item_7.01": "",
  "item_8.01": "",
  "item_9.01": "Item 9.01 Financial Statements and Exhibits.\n(d) Exhibits.\nExhibit\nNumber\nExhibit ...",
}
```

## Citation
An EDGAR-CRAWLER paper is on its way. Until then, please cite the relevant EDGAR-CORPUS paper published at the [3rd Economics and Natural Language Processing (ECONLP) workshop](https://lt3.ugent.be/econlp/) at EMNLP 2021 (Punta Cana, Dominican Republic):
```
@inproceedings{loukas-etal-2021-edgar,
    title = "{EDGAR}-{CORPUS}: {B}illions of {T}okens {M}ake {T}he {W}orld {G}o {R}ound",
    author = "Loukas, Lefteris  and
      Fergadiotis, Manos  and
      Androutsopoulos, Ion  and
      Malakasiotis, Prodromos",
    booktitle = "Proceedings of the Third Workshop on Economics and Natural Language Processing",
    month = nov,
    year = "2021",
    address = "Punta Cana, Dominican Republic",
    publisher = "Association for Computational Linguistics",
    url = "https://aclanthology.org/2021.econlp-1.2",
    pages = "13--18",
}
```
Read the paper here: [https://aclanthology.org/2021.econlp-1.2/](https://aclanthology.org/2021.econlp-1.2/)

## Accompanying Resources
- [EDGAR-CORPUS on Zenodo] EDGAR-CORPUS: The biggest corpus for financial NLP research, built from `EDGAR-CRAWLER` - [https://zenodo.org/record/5528490](https://zenodo.org/record/5528490)
- [EDGAR-CORPUS on HuggingFace 🤗 datasets] -[https://huggingface.co/datasets/eloukas/edgar-corpus/](https://huggingface.co/datasets/eloukas/edgar-corpus/)
- [Financial Word2Vec Embeddings] EDGAR-W2V: Word2vec Embeddings trained on EDGAR-CORPUS - [https://zenodo.org/record/5524358](https://zenodo.org/record/5524358)

## Contributing
PRs and contributions are accepted.
 
Please use the [Feature Branch Workflow](https://www.atlassian.com/git/tutorials/comparing-workflows/feature-branch-workflow).

## Issues
Please create an issue on GitHub instead of emailing us directly so all possible users can benefit from the troubleshooting.

## License
This software is licensed under the [GNU General Public License v3.0](https://github.com/nlpaueb/edgar-crawler/blob/main/LICENSE), a license approved by the Open-Source Initiative (OSI).

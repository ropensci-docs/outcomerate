# middleearth Dataset

`middlearth` is a toy dataset consisting of 1691 fake survey interviews
conducted in J.R.R. Tolkien's fictional world of Middle Earth.

## Details

Variables contained in the data:

- **code:** one of the outcome codes `I`, `P`, `R`, `NC`, `O`, `UH`,
  `UO`, and `NE`

- **outcome:** A human-interpretable label for the `code` variable

- **researcher**: An identifier for the researcher conducting the
  interview

- **region**: The region of the respondent (one of five)

- **Q1**: A hypothetical binary research question posed to respondents

- **Q2**: A hypothetical continuous scale question posed to respondents

- **day**: The day the interview took place (1 being the first day of
  fieldwork)

- **race**: The race of the respondent in middle earth (Dwarf, Elf,
  Hobbit, Man, or Wizard)

- **svywt**: The survey weight (inverse probability of selection)

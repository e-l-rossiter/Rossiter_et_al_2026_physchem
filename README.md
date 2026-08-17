# Rossiter_et_al_2026_physchem
Code and analysis for: Rossiter et al. unpublished: A novel approach to identify aquatic pollutants that could select for antimicrobial resistance: compound prioritisation for risk assessment research

This repository contains the final datasets, analyses and figures of the above-mentioned paper.  It can recreate all figures and tables in both the main text and the supplementary files.  

In the file "Scripts" you will find the R Scripts used in this paper. 

You will need to run each script in order. 

**Purpose of each script:**

**Script 1**
This script creates a PubChem reference table for the pollutants and antibiotics of interest, so that their SMILES strings can be used later to calculate physicochemical properties on Chemicalize.com. The script also selects only compounds with MW <= 600 Da, as we did not want to compute physicochemical properties for compounds that were not of interest.

 The script first defines the compound lists used in the study, including:

 - pollutants of interest the sources of which are referenced in the paper
 - antibiotics from O'Shea, Rosemarie, and Heinz E. Moser:
 "Physicochemical properties of antibacterial compounds: implications for
 drug discovery." Journal of Medicinal Chemistry,
 doi:10.1021/jm700967e
 - additional antibiotics from other sources which are referenced in the paper

PubChem is searched using the webchem package to retrieve PubChem compound identifiers, known as CIDs, for each compound name. Where PubChem does not automatically return a CID, we manually add the correct CID for Polymyxin B1. Using the PubChem CIDs, the webchem package retrieves compound information from PubChem, including:

 - SMILES
 - molecular weight
 - CAS numbers
   
The webchem package is also used to retrieve PubChem synonyms for each compound. We then extract the first valid CAS Registry Number where available.

The final PubChem information table, containing data extracted from PubChem,
includes:

- Compound_Name
- PubChem CID
- SMILES
- molecular weight (g/mol)
- CAS number
- CAS number with dashes removed

 The compounds are split into two groups based on molecular weight:
 
 - compounds with molecular weight less than or equal to 600 g/mol
 - compounds with molecular weight greater than 600 g/mol
   
This is because we are only interested in compounds that are <= 600 Da.
600 Da is exactly equivalent to 600 g/mol. Finally, the PubChem information is saved into one Excel workbook with three
sheets:

- All_comps: all compounds with PubChem information
- Comps_less_or_equal_600_g_mol: compounds with MW <= 600 g/mol
 - Comps_greater_than_600_g_mol: compounds with MW > 600 g/mol

These outputs are used later for bulk upload to Chemicalize.com, to calculate physicochemical properties using the SMILES strings in the web-based application.

**NOTE for anyone wanting to use Chemicalize:**
Unfortunately, ChemAxon is retiring Chemicalize on 30 June 2027. 

**Script 2**
Purpose:
This script uses the PubChem information generated in Script 1 to prepare input files for Chemicalize.com and eNTRYway.

First, the PubChem workbook is read in and the compounds with MW <= 600 g/mol
are selected. These are the compounds that we want to use for physicochemical
property calculations.

A text file containing SMILES and compound names is then created for bulk
upload to Chemicalize.com. The Chemicalize output is downloaded as an SDF file and read back into R using ChemmineR.

The Chemicalize SDF data block is converted into a table containing the calculated physicochemical properties. The Chemicalize SMILES are then used to create a second text file for bulk upload to eNTRYway.The eNTRYway output is read back into R as a CSV file. Compound names are then checked and renamed where needed, because spaces were removed from compound names during the upload process. Finally, the Chemicalize output, eNTRYway output and PubChem information are
joined together by compound name. The final joined table is saved as an Excel workbook for later analysis.

**Script 3**

This script uses the final joined PubChem, Chemicalize and eNTRYway output created in Script 2
The script calculates additional physicochemical variables needed for theanalysis, including:

- interpolated logD values at  7.4
- relative polar surface area
- hydrophilic compound status
- low globularity status
- low flexibility status
- eNTRY compound status

Chemicalize bulk predictions only returned cLogD values at integer pH values, so logD at pH 7.4 was calculated by linear interpolation between logD at pH 7 and logD at pH 8. 

Ionisation at pH 7.4 was described using compound net charge, and the major microspecies was defined as the species present at the highest fractional abundance at pH 7.4. As these values were not included in the Chemicalize bulk upload, they were manually transcribed from the Chemicalize web-based interface and joined into the main data frame.

For compounds ionised at pH 7.4 but lacking an amine, the presence of an ionisable nitrogen was also assessed manually. These manually checked functional-group assignments were then joined into the main data frame.

The script also joins compound use/class information so that pollutants and antibiotics can be separated and summarised by compound class.

The final data frame is then used to assign compounds into:

- Group 1
- Group 2
- Group 3
- No group
- 
These groups are based on hydrophilicity, eNTRY rule status and charge state at pH 7.4. Finally, the script creates separate tables for pollutants and antibiotics in each group, checks the compounds assigned to each group, and saves the final physicochemical-property table as an Excel file.

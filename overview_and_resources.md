** Problem Statement: ** We will use the MIMIC III ICU database, which contains all recorded information pertaining to patient admissions (~40,000 total patients) at Beth Israel Hospital in Boston from 2001-2012. We will work on predicting mortality risk of patients based on variables related to the care of these patients throughout their hospital stay.

## Navigating MIMIC website
	- Tables in MIMIC (26 tables): https://mimic.physionet.org/mimictables/admissions/

## MIMIC Github: 
	https://github.com/MIT-LCP/mimic-code

	- Scripts to build Postgres database
		- Tutorial to build database: http://mimic.physionet.org/tutorials/install-mimic-locally-ubuntu/
	- Include severity scores like OASiS, SOFA, SAPS
	- Defining comorbidity status for patients

## Resources

#### Current web visualization of data 
(NOTE: very similar, but not exact same dataset i.e. MIMIC 2 vs 3):
	http://hdsl.uwaterloo.ca/visualization-tool/

#### Predicting ICU mortality using variety of machine learning:
(NOTE: not same dataset)
https://www.physionet.org/challenge/2012/papers/

#### Similar ICU study from UCSF
	http://healthpolicy.ucsf.edu/content/icu-outcomes
- Seemingly some sort of regression, they explored variables interactions.

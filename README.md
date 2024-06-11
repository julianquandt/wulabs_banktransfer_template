# Description

This repository contains a SoSciSurvey template to collect payout of participants from your study.
The survey can be referred to from your study (from e.g. an oTree, Qualtrics or Psychopy Study) to safely assess payout data seperately from the experimental data.
This repository contains two files, an English version of the payout template (`wulabs_bank_transfer_template_eng.xml`), and a German version (`wulabs_bank_transfer_template_ger.xml`). 
The two files are identical except for the language, so choose the one that matches the language of your experiment.

# Usage

The templates are designed to make their usage as easy as possible.
To make use of them follow these steps:

- Download the files by clicking [HERE](https://github.com/julianquandt/wulabs_soscisurvey_banktransfer_template/archive/refs/heads/main.zip) and unzip it. 
- Log into [SoSciSurvey](https://soscisurvey.wu.ac.at/) using your WU credentials.
- Click on `Project > Start new Survey Project` (see screenshot)
- Click on `Browse` to select one of the files (German or English version), and click on Continue.
- ![image](https://github.com/julianquandt/wulabs_soscisurvey_banktransfer_template/assets/24586635/5634b0ec-1032-4197-aade-d2f7dd208d77)
- Set a `Project Name` and `Project Folder` for your study. The latter one also determines the link to the survey and click on `ok`.
- ![image](https://github.com/julianquandt/wulabs_soscisurvey_banktransfer_template/assets/24586635/01da03a4-27b6-4dde-bdc8-c047f43b6f13)
- At this point, the project is ready to use. To test it, Click on `Compose Questionnaire` and click on the little green arrow button (when testing like this, no data will be saved, so make sure you test it again after activating the survey).
- ![image](https://github.com/julianquandt/wulabs_soscisurvey_banktransfer_template/assets/24586635/c51f85ee-9b2d-4fba-bd71-8d009daff467)
- To activate the survey, click on `Survey Project > Project Settings` and then on `Release The Survey`. On this page, set an `Administration Period`. Afterwards, click on the Save button in the upper right corner.
- ![image](https://github.com/julianquandt/wulabs_soscisurvey_banktransfer_template/assets/24586635/8bb55bda-2d73-4901-9364-e16e343606cf)
- Use the link provided after activating to link to your survey after the experiment is completed.

# Functionality

## IBAN check

The survey automatically checks for the validity of entered IBANs. As FIBU can only process a list of IBANs that contains no faulty IBANs, this check is highly useful.
First, when participants enter their IBAN it is checked for country-specific IBAN rules, meaning that even small typos in the middle of the IBAN will often be identified.
Participants will be informed about this and asked to correct their IBAN. 
Once they submit the survey and the IBAN is still faulty, they will be asked to verify that they double-checked the IBAN and that they want to submit this potentially faulty IBAN.
This is mainly meant as a safeguard against some potential false positive errors in the IBAN checker and should not occur often.
Moreover, the IBAN is stripped from spaces and converted to uppercase, to make copy pasting to the FIBU bank transfer form as simple as possible for researchers.

## Where is there no field to enter the payment amount?

The survey does not include a field where participants enter their payment amount themselves (for obvious reasons). 
Instead, the amount should be retrieved from the experimental data, by transferring a participant identifier as URL parameters in the link referring to the survey. The details for different implementations (Qualtrics, oTree, Psychopy) are explained below.

The survey will detect the following URL parameters automatically, if passed, and store them in the data frame:

- `pid`
- `Prolific_PID` (useful when the study is run using prolific as it will automatically be included in the URL)
- `PanelID` (useful for some Panels that use this referrer)
- `payoff` (can be used to also transfer the payoff to the data frame, so you do not have to look it up in the experimental data for each participant; note that it is rather easy to change URL parameters and participants could potentially change their payoff entry in the data by changing the value in the url, so you should double check with the experimental data if in doubt).

 ### Referring from Qualtrics

When you run the rest of your study in Qualtrics, you should link to the survey on the last page of your Qualtrics study. For this, you can create a participant identifier in Qualtrics and pass it on to SoScisurvey as `pid` URL parameter. Detailed instructions about how to implement this can be found [here](https://www.qualtrics.com/support/survey-platform/survey-module/survey-flow/standard-elements/passing-information-through-query-strings/#PassingInformationFromASurvey)


 

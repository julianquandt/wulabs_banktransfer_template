# Description

This repository contains a SoSciSurvey template to collect payout of participants from your study.
The survey can be referred to from your study (from e.g. an oTree, Qualtrics or Psychopy Study) to safely assess payout data seperately from the experimental data.
This repository contains two files, an English version of the payout template (`wulabs_bank_transfer_template_eng.xml`), and a German version (`wulabs_bank_transfer_template_ger.xml`). 
The two files are identical except for the language, so choose the one that matches the language of your experiment.

# Table of Contents

- [Usage](#usage)
- [Functionality](#functionality)
  - [IBAN Check](#iban-check)
  - [Where is the field to enter the payment amount?](#where-is-the-field-to-enter-the-payment-amount)
    - [Referring from Qualtrics](#referring-from-qualtrics)
    - [Referring from oTree](#referring-from-otree)
    - [Referring from Psychopy](#referring-from-psychopy)

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
- To download the payout data, click on `Collected Data > Download Data` and click the `Download` option next to `Dataset`
- ![image](https://github.com/julianquandt/wulabs_soscisurvey_banktransfer_template/assets/24586635/34d5dd12-9c06-4a63-9843-6d2d7424647e)



# Functionality

## IBAN check

The survey automatically checks for the validity of entered IBANs. As FIBU can only process a list of IBANs that contains no faulty IBANs, this check is highly useful.
First, when participants enter their IBAN it is checked for country-specific IBAN rules, meaning that even small typos in the middle of the IBAN will often be identified.
Participants will be informed about this and asked to correct their IBAN. 
Once they submit the survey and the IBAN is still faulty, they will be asked to verify that they double-checked the IBAN and that they want to submit this potentially faulty IBAN.
This is mainly meant as a safeguard against some potential false positive errors in the IBAN checker and should not occur often.
Moreover, the IBAN is stripped from spaces and converted to uppercase, to make copy pasting to the FIBU bank transfer form as simple as possible for researchers.

## Where is the field to enter the payment amount?

The survey does not include a field where participants enter their payment amount themselves by default as participants could just enter whatever payout of course. 
If you still want to do this because referral is not an option, you can include a field for a participant ID that you can tell your participants in advance, and a payment field in the `Compose Questionnaire` in SoSciSurvey. 
The questions have already been pre-implemented and only need to be drag-and-dropped into the survey.
Preferably however, the amount should be retrieved from the experimental data, by transferring a participant identifier as URL parameters in the link referring to the survey. The details for different implementations (Qualtrics, oTree, Psychopy) are explained below.

The survey will detect the following URL parameters automatically, if passed, and store them in the data frame:

- `pid`
- `Prolific_PID` (useful when the study is run using prolific as it will automatically be included in the URL)
- `PanelID` (useful for some Panels that use this referrer)
- `payoff` (can be used to also transfer the payoff to the data frame, so you do not have to look it up in the experimental data for each participant; note that it is rather easy to change URL parameters and participants could potentially change their payoff entry in the data by changing the value in the url, so you should double check with the experimental data if in doubt).

To clarify, if any of these strings is found in the URL that refers to the survey, they will be stored in the data.
For example, if the participant opens a survey using the link `https://soscisurvey.wu.ac.at/my_supercool_experiment/?pid=1234&payoff=9.52`, the `1234` and `9.52` will automatically be saved.

 ### Referring from Qualtrics

When you run the rest of your study in Qualtrics, you should link to the survey on the last page of your Qualtrics study. For this, you can create a participant identifier in Qualtrics and pass it on to SoScisurvey as `pid` URL parameter. Detailed instructions about how to implement this can be found [here](https://www.qualtrics.com/support/survey-platform/survey-module/survey-flow/standard-elements/passing-information-through-query-strings/#PassingInformationFromASurvey).

### Referring from oTree

In otree, setting URL parameters can be done on the last page of the survey, by including an `payout_referral` app at the end of your study. To do this:

- Add `payout_referral` to the `app_sequence` list in the `SESSION_CONFIGS` variable in your project's `settings.py` file.
- Create a folder called `payout_referral` in your otree project directory and create a file called `__init.py__` in this directory that contains the following:

```
from otree.api import *

doc = """
This is a template for an exit survey linking to a SoSciSurvey payout survey.
"""

class C(BaseConstants):
    NUM_ROUNDS = 1
    PLAYERS_PER_GROUP = None
    NAME_IN_URL = 'payout_referral'

class Subsession(BaseSubsession):
    pass

class Group(BaseGroup):
    pass

class Player(BasePlayer):
    pass

# PAGES
class Payout_Refer_Page(Page):
    form_model = 'player'
    @staticmethod
    def js_vars(player: Player):
        payoff_eur = session.config['participation_fee']+player.participant.payoff.to_real_world_currency(session) # note that this needs to change to your specific payout variable.
        return dict(p_payoff = payoff_eur, p_participant = player.participant.code) # this returns the otree participant code, in case you are using something else, you need to change this.

page_sequence = [Payout_Refer_Page]
```

- Change the line that defines `payoff_eur` to include whatever bonus payment variable you are using for the participants in your experiment and, if needed, the `player.participant.code` to something else, if you do not want to use the oTree participant code as a pid identifer but instead e.g. a Qualtrics or Prolific code that you have passed into oTree at the start of the experiment.
- In the `payoff_referral` folder, create a file called `Payout_Refer_Page.html` and copy paste the following code into the file:

```
{{ extends 'global/Page.html' }}
{{ block title }}End of experiment{{ endblock }}

{{ block content }}

<div class="container">
    <p>
        We have reached the end of the experiment. Thank you very much for participating! 
    </p>
    <p>
        <b> In about 10 seconds, you will be redirected to a page where you need to enter your payment information.</b> Please do so, so we can pay you for your participation.
    </p>
</div>

{{ endblock }}

{{ block scripts }}
<script>
let link = 'https://soscisurvey.wu.ac.at/my_supercool_experiment/'; // replace the link with your own SoSciSurvey project. Make sure it ends with a / 
$(document).ready(function() {
    var payoff = js_vars.p_payoff;
    var pid = js_vars.p_participant; // this pid needs to change to whatever you pass on from the js_vars function in the __init__.py file of the payout_referral module
    window.setTimeout(function() {
        window.location.href = link.concat('?','payoff','=',payoff, '&', 'pid', '=', pid);
   }, 10000);});
</script>
{{ endblock }}

```
Thanks to Gabor Mozol for providing code for this template.

- Remove the `,payoff,'=',payoff, '&',` if you only want to include the pid but not payoff amount in the data in SoSciSurvey (the `'?'` needs to stay included).
- Change the text under `{{ block content }}` if you want the text to look different.
- If you want to wait shorter or longer before referring participants to the Payout Survey, change the number `10000` in the third-last line to a smaller or larger value (it is the wait time in milliseconds so 10000 = 10 sec).

SoSciSurvey will now detect that participants enter the survey with a defined `pid` (and `payoff` if you included it) variable in the URL and will automatically save these into the data.

### Referring from Psychopy

In psychopy, to refer to the survey, you would include a trial including only a `Code` component:
![image](https://github.com/julianquandt/wulabs_soscisurvey_banktransfer_template/assets/24586635/f7256554-a694-411e-9219-a40b5052abf4)
Double click on the code component once added to the trial and copy paste the following code into the left code field that will open up:

```
import webbrowser
pid = expInfo['participant']
if 'payoff' not in globals():
    payoff = 'NA'
link="https://soscisurvey.wu.ac.at/my_supercool_experiment/"+"?"+"pid="+str(pid)+"&payoff="+str(payoff)
webbrowser.open(link)
```

Again change the link to your own survey.
This code will automatically open the web browser after the psychopy experiment is completed, so it should always be the last part of your experiment.


 

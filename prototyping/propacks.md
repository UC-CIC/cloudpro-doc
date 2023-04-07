**Breadcrumb**

[Home](../home.md) > Prototyping Technical > Concepts > [PROPacks](propacks.md)

# PROPacks

PROPacks are the core concept of a extensible and collaborative PRO platform. PROPacks are zip files that include a colleciton of files that describe a specific PRO study.

## Pack Configuration Structure
The following is the expected file structure of a PROPack

```
study_name.zip
    study_name
        pack.config
        [language]
            questionnaire.json
            scoring.algo
            [physical]
                static_files
```

**pack.config** is utilized to configure the PROPack.  This file is in a format that is conducive to Python's Config package.

```
[MAIN]
LANUGAGES=EN
FORMAT=CPRO_R1
SCORING_FILE=scoring.algo
QUESTIONNAIRE_FILE=questionnaire.json
PHYSICAL=
```

**Languages:**  A comma separated list of languages in the pack.  It is expected that one folder for each language exists

**Format:** The format of the PROPack. Although this prototype only supports CPRO_R1 (a custom JSON structure for CloudPRO), it is anticipated that a productionalized version would support HL7 FHIR.

**Scoring File:** This contains the name of the scoring file.

**Questionnaire File:** This contains the name of the question file.

**Physical:** Although not in scope of this prototype, it is anticipated to have some form of physical PDF associated with the question set allowing for a fall back or alternative PRO study method.

## A Look into CPRO_R1 Format

CPRO_R1 is a custom format that CloudPRO is able to utilize. It aims to allow for ease of use to quickly define question sets for studies. 

Below is an example JSON file of the questionnaire.json:

```
{
  pro_pack: '9377b038eaec24cab37359e550d2f410c0623949264413b30770df83f6fd5164',
  pro_pack_format: 'CPRO_R1',
  data: {
    metadata: {
      name: 'Example Questionnaire',
      format: 'CPRO_R1',
      description: 'Please respond to each question or statement',
      version: '1.0',
    },
    questionnaire: [
      {
        data: {
          description: 'Select one',
          text: 'Gender',
          type: 'dropdown',
          value: ['male', 'female'],
          link_id: 'gender',
        },
        element: 'question',
      },
      {
        data: {
          scale: 'mm[Hg]',
          description: 'Type a number',
          text: 'Systolic blood pressure',
          type: 'decimal',
          value: 0,
          link_id: 'systolic_blood_pressure',
        },
        element: 'question',
      },
      {
        data: {
          description: 'Select one',
          text: 'Are you taking medication for high blood pressure?',
          type: 'radio',
          value: ['yes', 'no'],
          link_id: 'blood_pressure_med',
        },
        element: 'question',
      },
      {
        data: {
          description: 'Calculated field',
          text: '10-year HCHD Risk Probability',
          type: 'hidden',
          value: 0,
          link_id: 'hchd_risk_score',
        },
        element: 'question',
      },
      {
        data: {
          name: 'dyspnea',
          description: '',
          text: 'Over the past 7 days how short of breath did you get with each of these activities?',
          questions: [
            {
              type: 'checkbox',
              link_id: 'DYSSV001',
              text: 'Dressing yourself without help...',
              use_values: 'default',
            },
            {
              type: 'checkbox',
              link_id: 'DYSSV002',
              text: 'Walking 50 steps/paces on flat ground at a normal speed without stopping...',
              use_values: 'default',
            },
            {
              description: 'Calculated field',
              text: 'T Score',
              type: 'hidden',
              value: 0,
              link_id: 't_score',
            },
          ],
          values: {
            default: [
              {
                text: 'No shortness of breath',
                type: 'checkbox',
                value: 0,
              },
              {
                text: 'Mildly short of breath',
                type: 'checkbox',
                value: 1,
              },
              {
                text: 'Moderately short of breath',
                type: 'checkbox',
                value: 2,
              },
              {
                text: 'Severely short of breath',
                type: 'checkbox',
                value: 3,
              },
              {
                text: 'I did not do this in the past 7 days',
                type: 'checkbox',
                value: 'X',
              },
            ],
          },
        },
        element: 'group',
      },
      {
        data: {
          description: 'Testing',
          text: 'Test calculation',
          type: 'hidden',
          value: 0,
          link_id: 'test_score',
        },
        element: 'question',
      },
    ],
  },
}
```

This is a sample of a scoring.algo file:

```
[t_score]
formula=cpro_loader['systolic_blood_pressure']+10
```

## Deep dive into CPRO_R1 Structure
There are three required fields for every questionnaire set.

**pro_pack:** This is a unique SHA256 identifier for the question set.

**pro_pack_format:**  This designates the format of the question set; the prototype will only support CPRO_R1, but future production releases are anticipated to support HL7 FHIR.

**data:** This is the core information of the questionnaire.  It is a dictionary with multiple elements.

**data.metadata:** This defines metadata associated with the questionnaire set. Here things such as the human readable name, format, description, and version of the question set are defined.

**data.questionnaire[{qs},...]:** This is a list of all the questions (and render types) of a study.

**{qs}:** This specifically defines a question (or grouping of questions). The format to be followed is:

```
{
    data: {
        description: 'text here',
        text: 'text here',
        type: 'dropdown|radio|hidden|decimal|text',
        value: decimal|list,
        link_id: 'unique text',
    },
    element: 'question|group',
}
```

A {qs} can be specified as either **question** or **group**.  A question denotes that the data describes a single question.  A group describes that data will include multiple questions and a set of "default" values that are utilized by each question.

_First, let's look at the **question** e3lement type._

**Description:** helps describe what a question is (detailed text about a question).

**Text:** Specifies what should be displayed to end users.

**Type:** The prototype currently supports dropdowns, radio, hidden, text, and decimal types for display renders. Hidden fields will not render and are generally used as behind the scenes calculation fields.

**Value:** Value is generally a decimal value for types of decimal/hidden, text for types of text/hidden, and a list for types of dropdown & radio.

**Link ID:** Link ID is a unique identifier for the specific question. It is required to be a unique value across the entire JSON payload.

_Now, let's look at the **group** e3lement type._

A **group** structure is slightly different from a **question**.  A group begins with generic "metadata" fields.

**name:** This is the unique identifier for the grouping as a whole.

**description:** This is the description for the grouping as a whole.

**text:** This is the text prompt for the grouping as a whole.

Additionally, a list questions (following the typical question element type) is denoted with the **questions** field.

**Values** is a dictionary field of value sets that can be utilized by the grouping.  These value sets must follow the structure of supported types such as dropdown, radio, etc.

## A Look into scoring.algo

This file drives how various fields of a question set will be evaluated.  This follows the structure of typical Python config files.

Each section denotes the link_id that you will be evaluating. Each section must contain:

`formula={formula}`

where {formula} is standard python arithmetic operations.  If a field within the questionnaire is to be referenced, it should be the format of: 
`cpro_loader["LINK_ID"]`

Scoring algos are parsed through a CloudPRO module that evaluates safety of the formula before execution utilizing the Python AST module.
# 7. JATOS: URL variables for between counterbalancing

Here, for half of the participants, we want to switch the key response mapping.

- (1) key response mapping: `'red' -> 'f'`; `'blue' -> 'j'`
- (2) key response mapping: `'red' -> 'j'`; `'blue' -> 'f'`

Since online experimentation is not as controlled as lab experimentation, we can not control how
participants visit our page and if they finish the experiment. Therefore, to achieve perfect between
counterbalancing, we set up as many studies as there are between counterbalancing conditions (in
prolific) and recruit the same amount of participants for each condition. We could achieve this by
creating copies of the same experiment on JATOS as well, but a more convenient way is to use URL
variables. This way we can distribute the same link just with a different counterbalancing condition
as variable. We than use this variable on our single website to adjust the experiment according to
the condition.

The study links on prolific will then contain a `root` that is the same for all studies and a
condition variable, that is different for each counterbalancing condition:

- www.example-study.de?COUNTERBALANCE_CONDITION=0
- www.example-study.de?COUNTERBALANCE_CONDITION=1
- www.example-study.de?COUNTERBALANCE_CONDITION=2
- www.example-study.de?COUNTERBALANCE_CONDITION=3
- ...

*Note* In prolific, there can be other URL-variables added per default. A variable that is used
often is the PROLIFIC_PID of the participant. This can be added to the data and might be necessary
if, for example, participants are rewarded for good performance. To add more variables, we use `&`.
A typical prolific link looks like this:

www.example-study.de?PROLIFIC_PID={{ PROLIFIC_PID }}&STUDY_ID={{ STUDY_ID }}&SESSION_ID={{
SESSION_ID }}&COUNTERBALANCE_CONDITION=0

## Get the variable

First, we get the URL variable. And define a variable for the red response and the blue response.

```javascript
// ...

const counterbalanceCondition = parseInt(jatos.urlQueryParameters.COUNTERBALANCE_CONDITION) // get the url variable (the part after the urlQueryParamers.* has to match the name chosen int the link. Here, we also parse the parameter as INT since URL variables are typically interpreted as strings.

// Here, we also set the default (for example, if no COUNTERBALANCE_CONDITON is given). This is helpfull for testing since it is tedious to always add a URL variable when testing the experiment
let redKey = 'f'
let blueKey = 'j'
if (counterbalanceCondition === 1) {
    redKey = 'j'
    blueKey = 'f'
}


```

## Add counterbalancing logic

Here, we only need to adjust the feedback, but depending on the experiment, the counterbalancing
might need more adjustments (for example, in the instructions).

```javascript
const feedback = {
    type: jsPsychHtmlKeyboardResponse,
    stimulus: () => {
        const lastTrialResponse = jsPsych.data.get().last(1).values()[0].response
        const color = jsPsych.timelineVariable('color')
        if (color === 'red') {
            if (lastTrialResponse === redKey) {
                return 'CORRECT'
            } else if (lastTrialResponse === blueKey) {
                return 'FALSE'
            } else {
                return 'TOO SLOW'
            }
        } else if (color === 'blue') {
            if (lastTrialResponse === redKey) {
                return 'FALSE'
            } else if (lastTrialResponse === blueKey) {
                return 'CORRECT'
            } else {
                return 'TOO SLOW'
            }
        }
    },
    trial_duration: 1000,
    response_ends_trial: false,
}
```

## SUMMARY

### experiment.html

```html
<!DOCTYPE html>
<html>
<head>
    <title>My experiment</title>
    <script src="jatos.js"></script>
    <script src="https://unpkg.com/jspsych@7.3.4"></script>
    <script src="https://unpkg.com/@jspsych/plugin-html-keyboard-response@1.1.3"></script>
    <script src="trialSequences.js"></script>
    <link href="https://unpkg.com/jspsych@7.3.4/css/jspsych.css" rel="stylesheet" type="text/css"/>
</head>
<body></body>
<script>
    jatos.onLoad(function () {
        const counterbalanceCondition = parseInt(jatos.urlQueryParameters.COUNTERBALANCE_CONDITION)

        let redKey = 'f'
        let blueKey = 'j'
        if (counterbalanceCondition === 1) {
            redKey = 'j'
            blueKey = 'f'
        }

        let withinConditionNr = jatos.batchSession.get('withinConditionNr')
        if (withinConditionNr === undefined) {
            withinConditionNr = 0
        }
        jatos.batchSession.set('withinConditionNr', withinConditionNr + 1)

        const jsPsych = initJsPsych();

        const fixation = {
            type: jsPsychHtmlKeyboardResponse,
            stimulus: '+',
            trial_duration: 800,
            response_ends_trial: false
        }

        const stroopStimulus = {
            type: jsPsychHtmlKeyboardResponse,
            stimulus: () => {
                const color = jsPsych.timelineVariable('color')
                const word = jsPsych.timelineVariable('word')
                return `<div style="color: ${color}">${word}</div>`
            },
            choices: ['f', 'j'],
            trial_duration: 2500
        }

        const feedback = {
            type: jsPsychHtmlKeyboardResponse,
            stimulus: () => {
                const lastTrialResponse = jsPsych.data.get().last(1).values()[0].response
                const color = jsPsych.timelineVariable('color')
                if (color === 'red') {
                    if (lastTrialResponse === redKey) {
                        return 'CORRECT'
                    } else if (lastTrialResponse === blueKey) {
                        return 'FALSE'
                    } else {
                        return 'TOO SLOW'
                    }
                } else if (color === 'blue') {
                    if (lastTrialResponse === redKey) {
                        return 'FALSE'
                    } else if (lastTrialResponse === blueKey) {
                        return 'CORRECT'
                    } else {
                        return 'TOO SLOW'
                    }
                }
            },
            trial_duration: 1000,
            response_ends_trial: false,
        }

        const trial = {
            timeline: [fixation, stroopStimulus, feedback],
            timeline_variables: trialSequences[withinConditionNr]
        }

        const timeline = [trial]

        jsPsych.run(timeline)
    });
</script>
</html>
```

[Home](index.md) [Next](jatosUploadData.md)

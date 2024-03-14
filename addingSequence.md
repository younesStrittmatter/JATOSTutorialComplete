# 5. jsPsych: Adding a sequence

There are two ways to add the generated trial sequences. First, we can just copy the object into our
script:

```html

<script>
    // ...
    const trialSequences = [[...]]
</script>
```

A more convenient way is to copy the file `trialSequence.js` into the JATOS folder and add the
following to the experiment line to the `experiment.html`.

```html

<head>
    <title>My experiment</title>
    <script src="https://unpkg.com/jspsych@7.3.4"></script>
    <script src="https://unpkg.com/@jspsych/plugin-html-keyboard-response@1.1.3"></script>
    <!-- import the trialSequence from the trialSequence.js file -->
    <script src="trialSequences.js"></script>
    <link href="https://unpkg.com/jspsych@7.3.4/css/jspsych.css" rel="stylesheet" type="text/css"/>
</head>
```

In both cases, we now have a list of feature sequences that we can use as timeline variables. Let's
use the first sequence in our experiment.

```javascript
// ...
const trial = {
    timeline: [fixation, stroopStimulus, feedback],
    timeline_variables: trialSequences[0]
}
// ...
```

## SUMMARY

Our JATOS folder now should contain two files: `experiment.html` and `trialSequences.js`.
The content of the files should look like this:

### trialSequences.js

```javascript
const trialSequences = [[{
    "color": "red",
    "word": "RED",
    "congruency": "congruent",
    "response_transition": ""
}, ...]]
```

### experiment.html
```html
<!DOCTYPE html>
<html>
<head>
    <title>My experiment</title>
    <script src="https://unpkg.com/jspsych@7.3.4"></script>
    <script src="https://unpkg.com/@jspsych/plugin-html-keyboard-response@1.1.3"></script>
    <script src="trialSequences.js"></script>
    <link href="https://unpkg.com/jspsych@7.3.4/css/jspsych.css" rel="stylesheet" type="text/css"/>
</head>
<body></body>
<script>
    const jsPsych = initJsPsych();

    const fixation = {
        type: jsPsychHtmlKeyboardResponse,
        stimulus: '+',
        trial_duration: 800,
        response_ends_trial: false,
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
                if (lastTrialResponse === 'f') {
                    return 'CORRECT'
                } else if (lastTrialResponse === 'j') {
                    return 'FALSE'
                } else {
                    return 'TOO SLOW'
                }
            } else if (color === 'blue') {
                if (lastTrialResponse === 'f') {
                    return 'FALSE'
                } else if (lastTrialResponse === 'j') {
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
        timeline_variables: trialSequences[0]
    }
    
    const timeline = [trial]

    jsPsych.run(timeline)
</script>
</html>
```

[Home](index.md) [Next](jatosUsingVariables.md)

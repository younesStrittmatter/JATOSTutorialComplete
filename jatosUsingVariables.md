# 6. JATOS: Using JATOS variables

A problem with the previous example is that each participant will get the same feature sequence.
Here, we fix this by using JATOS batch variables. These variables are shared between all
participants that visit our online experiment. Each time a participant visits the website, we will
increase a counter and use the counter to pick the feature sequence.

*Note:*
Since we are only using one feature sequence per participant, we increase the counter by one. If we
want to use multiple sequences (e.g., for multiple blocks), we would increase the counter by the
number of blocks and use the indices `counter, counter + 1, counter + 2, ..., counter + nrOfBlocks`.

**!!!Warning!!!**

It is not advised to use the counter for between counterbalancing since participants will visit the
page without completing the experiment. This will lead to unbalanced data (for between subject
counterbalancing)

## Import JATOS functionality

To use JATOS functionality, we need to import it to our script just like jsPsych in the `<head>`
portion of the `experiment.html` file.

```html
<!-- ... -->
<head>
    <title>My experiment</title>
    <!-- import the jatos -->
    <script src="jatos.js"></script>
    <script src="https://unpkg.com/jspsych@7.3.4"></script>
    <script src="https://unpkg.com/@jspsych/plugin-html-keyboard-response@1.1.3"></script>
    <script src="trialSequences.js"></script>
    <link href="https://unpkg.com/jspsych@7.3.4/css/jspsych.css" rel="stylesheet" type="text/css"/>
</head>
<!-- ... -->
```

## Wrap jsPsych code into JATOS

To use JATOS functionality, we also want to completely load a component before running jsPsych code.
To achieve this, we wrap our jsPsych code into an onload callback of JATOS

```html
<script>
    // Wrap the entire code into a jatos.onLoad callback function
    jatos.onLoad(function () {
        const jsPsych = initJsPsych();

        const fixation = {
            type: jsPsychHtmlKeyboardResponse,
            stimulus: '+',
            trial_duration: 800
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
            trial_duration: 1000
        }

        const trial = {
            timeline: [fixation, stroopStimulus, feedback],
            timeline_variables: trialSequences[0]
        }

        const timeline = [trial]

        jsPsych.run(timeline)
    });
</script>
```

## JATOS batch variable
There are multiple session variables: batch, group and study variables. Here, we are interested in batch variables. These will be shared by the whole batch of participants that are recruited for a single study.

At the start of our script, we will load a batch variable that we named withinConditionNr. We then add one to this variable for the next participant and use it to index our trialSequences.

```javascript
// ...
jatos.onLoad(function () {
    let withinConditionNr = jatos.batchSession.get('withinConditionNr') // get the number
    if (withinConditionNr === undefined) { // handle the case when this is not initialized yet for the first participant
        withinConditionNr = 0
    }
    jatos.batchSession.set('withinConditionNr', withinConditionNr + 1) // upload the increased number
    
    // ...
    
    const trial = {
            timeline: [fixation, stroopStimulus, feedback],
            timeline_variables: trialSequences[withinConditionNr] // replace the zero with the withinConditionNr
        }
});
// ...
```

**!!! WARNING !!!**

Since participants start the experiment without finishing it, it is advised to upload more feature sequences than necessary (double or even triple the number of participants)

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
        let withinConditionNr = jatos.batchSession.get('withinConditionNr')
        if (withinConditionNr === undefined) {
            withinConditionNr = 0
        }
        jatos.batchSession.set('withinConditionNr', withinConditionNr + 1) 
        
        const jsPsych = initJsPsych();

        const fixation = {
            type: jsPsychHtmlKeyboardResponse,
            stimulus: '+',
            trial_duration: 800
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
            trial_duration: 1000
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

[Home](index.md) [Next]()
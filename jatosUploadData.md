# 8. JATOS: Upload Data

After a participant has finished the experiment, we want to upload the data to JATOS. For this, the only thing we need to do is tell jsPsych to upload the data on finish:

```javascript
const jsPsych = initJsPsych({
  on_finish: () => jatos.endStudy(jsPsych.data.get().json())
});
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
        let withinConditionNr = jatos.batchSession.get('withinConditionNr')
        if (withinConditionNr === undefined) {
            withinConditionNr = 0
        }
        jatos.batchSession.set('withinConditionNr', withinConditionNr + 1) 
        
        const jsPsych = initJsPsych({
            on_finish: () => jatos.endStudy(jsPsych.data.get().json())
        });

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

[Home](index.md)
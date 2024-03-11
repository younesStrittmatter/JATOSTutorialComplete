# 3. jsPsych: Adding timeline variables
In the previous example, we created a stimulus sequence. Here, we will create a trial sequence in a convenient way using timeline variebles.
## (1) Bundling the stimulus sequence

Let's first bundle our existing stimuli sequence into a single trial.
```html
<!DOCTYPE html>
<html>
  <head>
    <title>My experiment</title>
    <script src="https://unpkg.com/jspsych@7.3.4"></script>
    <script src="https://unpkg.com/@jspsych/plugin-html-keyboard-response@1.1.3"></script>
    <link href="https://unpkg.com/jspsych@7.3.4/css/jspsych.css" rel="stylesheet" type="text/css" />
  </head>
  <body></body>
  <script>
    const jsPsych = initJsPsych();
    
    const fixation = {
        type: jsPsychHtmlKeyboardResponse,
        stimulus: '+',
        trial_duration: 800
    }
    
    const stroopStimulus = {
      type: jsPsychHtmlKeyboardResponse,
      stimulus: '<div style="color: blue">RED</div>',
      choices: ['f', 'j'],
      trial_duration: 2500
    }
    
    const feedback = {
        type: jsPsychHtmlKeyboardResponse,
        stimulus: () => {
            const lastTrialResponse = jsPsych.data.get().last(1).values()[0].response
            if (lastTrialResponse === 'f') {
                return 'FALSE'
            } else if (lastTrialResponse === 'j') {
                return 'CORRECT'
            } else {
                return 'TOO SLOW'
            }
        },
        trial_duration: 1000
    }
    
    // Bundle the stimuli into a trial
    const trial = {
        timeline: [fixation, stroopStimulus, feedback]
    }
    
    // In the timeline, we now have a single trial
    const timeline = [trial]
    
    jsPsych.run(timeline)  
  </script>
</html>
```

## (2) Defining the trial sequence
Instead of changing the trials each time, we will use timeline variables to create the sequence. First we define the sequence as a list. Here, each trial will have two features that change: the color of the word and it's meaning. We first define a list for these features.

```javascript
// ...
const featureSequence = [
    {color: 'red', word: 'RED'},
    {color: 'blue', word: 'BLUE'},
    {color: 'red', word: 'BLUE'},
    {color: 'blue', word: 'BLUE'},
    {color: 'blue', word: 'RED'}
]

// ...
```

## (3) Adding timeline variables to the trial
We add this as timeline variables to the trial

```javascript
// ...

const featureSequence = [
    {color: 'red', word: 'RED'},
    {color: 'blue', word: 'BLUE'},
    {color: 'red', word: 'BLUE'},
    {color: 'blue', word: 'BLUE'},
    {color: 'blue', word: 'RED'}
]

// ...

// Add the feature sequence to the trial
const trial = {
        timeline: [fixation, stroopStimulus, feedback],
        timeline_variables: featureSequence
    }
```

## (4) Adding timeline variables to the stimuli
We also need to "tell" the various stimuli how to use the timeline stimuli. 

### (4.1) Stroop stimulus
We need to alter the stroopStimulus part.

```javascript
// ...

const stroopStimulus = {
      type: jsPsychHtmlKeyboardResponse,
      stimulus: () => {
          const color = jsPsych.timelineVariable('color') // get the color from the timeline
          const word = jsPsych.timelineVariable('word') // get the word from the timeline
          return `<div style="color: ${color}">${word}</div>` // use the variables in the stimulus.
      },
      choices: ['f', 'j'],
      trial_duration: 2500
    }

// ...
```

### (4.2) Feedback
We also need to alter the feedback to use the color from the feature timeline

```javascript
// ...

const feedback = {
        type: jsPsychHtmlKeyboardResponse,
        stimulus: () => {
            const lastTrialResponse = jsPsych.data.get().last(1).values()[0].response
            const color = jsPsych.timelineVariable('color') // get the color from the timeline
            if (color === 'red') { // add logic if the the color is red
                if (lastTrialResponse === 'f') {
                    return 'CORRECT'
                } else if (lastTrialResponse === 'j') {
                    return 'FALSE'
                } else {
                    return 'TOO SLOW'
                }
            } else if (color === 'blue') { // add logic if the color is blue
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
    
// ...
```

## Finished Stroop Task

Our code for a simple stroop task is now finished. If we want to add trials or change them, we only need to change the feature timeline. The rest of the code will remain the same

```html
<!DOCTYPE html>
<html>
  <head>
    <title>My experiment</title>
    <script src="https://unpkg.com/jspsych@7.3.4"></script>
    <script src="https://unpkg.com/@jspsych/plugin-html-keyboard-response@1.1.3"></script>
    <link href="https://unpkg.com/jspsych@7.3.4/css/jspsych.css" rel="stylesheet" type="text/css" />
  </head>
  <body></body>
  <script>
    const jsPsych = initJsPsych();
    
    const featureSequence = [
        {color: 'red', word: 'RED'},
        {color: 'blue', word: 'BLUE'},
        {color: 'red', word: 'BLUE'},
        {color: 'blue', word: 'BLUE'},
        {color: 'blue', word: 'RED'}
    ]
    
    const fixation = {
        type: jsPsychHtmlKeyboardResponse,
        stimulus: '+',
        trial_duration: 800
    }
    
    const stroopStimulus = {
      type: jsPsychHtmlKeyboardResponse,
      stimulus: () => {
          const color = jsPsych.timelineVariable('color') // get the color from the timeline
          const word = jsPsych.timelineVariable('word') // get the word from the timeline
          return `<div style="color: ${color}">${word}</div>` // use the variables in the stimulus.
      },
      choices: ['f', 'j'],
      trial_duration: 2500
    }
    
    const feedback = {
        type: jsPsychHtmlKeyboardResponse,
        stimulus: () => {
            const lastTrialResponse = jsPsych.data.get().last(1).values()[0].response
            const color = jsPsych.timelineVariable('color') // get the color from the timeline
            if (color === 'red') { // add logic if the the color is red
                if (lastTrialResponse === 'f') {
                    return 'CORRECT'
                } else if (lastTrialResponse === 'j') {
                    return 'FALSE'
                } else {
                    return 'TOO SLOW'
                }
            } else if (color === 'blue') { // add logic if the color is blue
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
        timeline_variables: featureSequence
    }
    
    // In the timeline, we now have a single trial
    const timeline = [trial]
    
    jsPsych.run(timeline)  
  </script>
</html>
```
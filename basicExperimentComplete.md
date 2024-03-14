Here is the full code for a perfectly counterbalanced stroop experiment

### Experiment.html
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
            timeline_variables: trialSequences[withinConditionNr]
        }

        const timeline = [trial]

        jsPsych.run(timeline)
    });
</script>
</html>
```

### createSequence.py
```python
# Imports
import json
from sweetpea import (Factor, DerivedLevel, WithinTrial, Transition, CrossBlock, 
                      MinimumTrials, synthesize_trials, CMSGen, experiments_to_dicts)

# Regular factors
color = Factor(name='color', initial_levels=['red', 'blue'])
word = Factor(name='word', initial_levels=['RED', 'BLUE'])

# Derived factors
## Congruency
def is_congruent(col, wrd):
    return col == wrd.lower()

def is_incongruent(col, wrd):
    return not is_congruent(col, wrd)

congruent = DerivedLevel('congruent', WithinTrial(is_congruent,   [color, word]))
incongruent = DerivedLevel('incongruent', WithinTrial(is_incongruent,   [color, word]))

congruency = Factor(name='congruency', initial_levels=[congruent, incongruent])

## Response transition
def is_response_repetition(col):
    return col[0] == col[-1]

def is_response_switch(col):
    return not is_response_repetition(col)

response_repetition = DerivedLevel('response_repetition', Transition(is_response_repetition, [color]))
response_switch = DerivedLevel('response_switch', Transition(is_response_switch, [color]))

response_transition = Factor(name='response_transition', initial_levels=[response_repetition, response_switch])


# Constraints
minimum_trials_constraint = MinimumTrials(20)

# Experimental design
design = [color, word, congruency, response_transition]
crossing = [color, word, response_transition]
constraints = [minimum_trials_constraint]

block = CrossBlock(design=design, crossing=crossing, constraints=constraints)

# Sequence synthesis and export
experiment = synthesize_trials(block=block, samples=5, sampling_strategy=CMSGen)

as_dicts = experiments_to_dicts(block, experiment)

with open('trialSequences.js', 'w') as f:
    f.write('const trialSequences = ')
    json.dump(as_dicts, f)
```
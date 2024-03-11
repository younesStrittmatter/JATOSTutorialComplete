# 2. jsPsych: Single Stroop trial
Here, we learn how to create a stimulus sequence that comprises a trial in a stroop experiment.
## (1) Barebone html file

There's some basic code that (nearly) all HTML documents have in common. Here's a typical bare-bones HTML document.
```html
<!DOCTYPE html>
<head>
    <title>Title</title>
</head>
<body>

</body>
</html>
```
To start, copy this code into your `experiment.html` file

## (2) Title tag
First, we can change the title of this page. This will be shown in the browser's title bar or in the page's tab
```html
<!DOCTYPE html>
<head>
    <!-- Changed title --->
    <title>My experiment</title>
</head>
<body>

</body>
</html>
```

## (3) Import functionality from jsPsych

To use jsPsych, add a `<script>` tag to load the library in the `<head>`. We'll load the library from a CDN, which means that the library is hosted on another server and can be loaded without having your own copy.
```html
<!DOCTYPE html>
<html>
  <head>
    <title>My experiment</title>
    <!-- Import jsPsych -->  
    <script src="https://unpkg.com/jspsych@7.3.4"></script>
  </head>
  <body></body>
</html>
```

We also want to import the jsPsych stylesheet, which applies a basic styling to the experiment. This requires adding a `<link>` tag to the `<head>` section of the document.
```html
<!DOCTYPE html>
<html>
  <head>
    <title>My experiment</title>
    <script src="https://unpkg.com/jspsych@7.3.4"></script>
    <!-- Import jsPsych stylesheet -->  
    <link href="https://unpkg.com/jspsych@7.3.4/css/jspsych.css" rel="stylesheet" type="text/css" />
  </head>
  <body></body>
</html>
```

## (4) Initializing jsPsych

To add JavaScript code directly to the webpage we need to add a pair of `<script>` tags after the `<body>` tags.
```html
<!DOCTYPE html>
<html>
  <head>
    <title>My experiment</title>
    <script src="https://unpkg.com/jspsych@7.3.4"></script>
    <link href="https://unpkg.com/jspsych@7.3.4/css/jspsych.css" rel="stylesheet" type="text/css" />
  </head>
  <body></body>
  <!-- Add script tags -->
  <script>
  </script>
</html>
```

To initialize jsPsych we use the `initJsPsych()` function and assign the output to a new variable.
```html
<!DOCTYPE html>
<html>
  <head>
    <title>My experiment</title>
    <script src="https://unpkg.com/jspsych@7.3.4"></script>
    <link href="https://unpkg.com/jspsych@7.3.4/css/jspsych.css" rel="stylesheet" type="text/css" />
  </head>
  <body></body>
  <script>
    // Initialize jsPsych  
    const jsPsych = initJsPsych();
  </script>
</html>

```

## (5) Use a plugin to create the task
### (5.1) Import the plugin

For a Stroop task, we want to show text on the screen. This is exactly what the `html-keyboard-response plugin` is designed to do. To use the plugin, we need to load it with a `<script>` tag.
```html
<!DOCTYPE html>
<html>
  <head>
    <title>My experiment</title>
    <script src="https://unpkg.com/jspsych@7.3.4"></script>
    <!-- Import the plugin -->  
    <script src="https://unpkg.com/@jspsych/plugin-html-keyboard-response@1.1.3"></script>
    <link href="https://unpkg.com/jspsych@7.3.4/css/jspsych.css" rel="stylesheet" type="text/css" />
  </head>
  <body></body>
  <script>
    const jsPsych = initJsPsych();
  </script>
</html>
```

### (5.2) Define the trial

Now, let us define our first trial. The type is `jsPsychHtmlKeyboardResponse` and the stimulus is the word `RED`.
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
    
    // Define our trial
    const stroopStimulus = {
      type: jsPsychHtmlKeyboardResponse,
      stimulus: 'RED'
    }
  </script>
</html>
```

### (5.3) Run the experiment

To run the experiment, we add the trials to a timeline and run the timeline.
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
    
    const stroopStimulus = {
      type: jsPsychHtmlKeyboardResponse,
      stimulus: 'RED'
    }
    
    // Define the timeline
    const timeline = [stroopStimulus]
    
    // Run the experiment
    jsPsych.run(timeline)  
  </script>
</html>
```

### (5.4) Refine the task

We want the stimulus to be shown in a color (for example, blue). Instead of a string, the stimulus parameter of a `jsPsychHtmlKeyboardResponse` trial also accepts html code. Let's use a div tag and colorize it.
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
    
    const stroopStimulus = {
      type: jsPsychHtmlKeyboardResponse,
      stimulus: '<div style="color: blue">RED</div>' // Use html code instead of a string 
    }
    
    const timeline = [stroopStimulus]
    
    jsPsych.run(timeline)  
  </script>
</html>
```

Let's also add choices for the participant to respond. Let's say `F` with the left index finger to respond to `red` stimuli and `J` with the right index finger to respond to `blue` stimuli.
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
    
    const stroopStimulus = {
      type: jsPsychHtmlKeyboardResponse,
      stimulus: '<div style="color: blue">RED</div>',
      choices: ['f', 'j'] // Add choices
    }
    
    const timeline = [stroopStimulus]
    
    jsPsych.run(timeline)  
  </script>
</html>
```

We also want the stimulus to be shown for a specific time. Let's say 2500ms. We can add this as parameter.
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
    
    const stroopStimulus = {
      type: jsPsychHtmlKeyboardResponse,
      stimulus: '<div style="color: blue">RED</div>',
      choices: ['f', 'j'],
      trial_duration: 2500 // Add trial duration
    }
    
    const timeline = [stroopStimulus]
    
    jsPsych.run(timeline)  
  </script>
</html>
```

## (6) A complete Trial: Adding fixation cross and feedback
### (6.1) Add a fixation cross

Let's add a fixation cross before the Stroop stimulus is shown. We just have to define another trial without any choices and with a + as stimulus. Then we add this fixation cross to the timeline.
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
    
    // Create a fixation cross
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
    
    // Add the fixation to the timeline
    const timeline = [fixation, stroopStimulus]
    
    jsPsych.run(timeline)  
  </script>
</html>
```

### (6.2) Add feedback

We also want to add feedback. Remember: If the color is `blue`, the participant should press `J`. Here, we introduce a new concept. We can access data from previous trials. Depending on the data, we show a different stimulus. In this case, the stimulus parameter of the trial has to be a function.
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
    
    // Add feedback
    const feedback = {
        type: jsPsychHtmlKeyboardResponse,
        stimulus: () => { // Whenever a trial parameter is dynamically adjusted on runtime, it has to be a function
            const lastTrialResponse = jsPsych.data.get().last(1).values()[0].response // Here, we access the data of the last trial, especially the response field
            if (lastTrialResponse === 'f') {
                return 'FALSE' // if the participant pressed f, we present the text FALSE
            } else if (lastTrialResponse === 'j') {
                return 'CORRECT' // if the participant pressed j, we present the text CORRECT
            } else {
                return 'TOO SLOW' // if the response was neither f nor j we present the text TOO SLOW
            }
        },
        trial_duration: 1000
    }
    
    // Add the feedback to the timeline
    const timeline = [fixation, stroopStimulus, feedback]
    
    jsPsych.run(timeline)  
  </script>
</html>
```
Here, we learned how to create a single stimulus sequence. The next tutorial will show how we can chain these together to create a block.

[Home](index.md) [Next]()






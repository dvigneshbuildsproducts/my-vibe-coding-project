# PROMPTS.md: Living Prompt Pack

> Module 3 · Prompt Chaining. Re-architect the build with prompt chains; capture the reusable ones here.

## How to use this pack

_Each prompt is a reusable step. Chain them: the output of one becomes the input to the next._

## Prompt chain: Retention Engine Hardening Chain

### Step 1: Expand, build new screens in a strict sequence
```
Build the next phase of this app in a strict sequence:
1. Add a screen team workspace once the task and invite are done. Match the layout and spacing of the attached {{reference A}} screenshot.
2. Add a screen PM Dashboard to share the analytics of the experiment. Match the data density of the attached {{reference B}} screenshot.
3. Navigation: write the logic so Team workspace links to PM Dashboard

Build these in order so workspace screen is the anchor for PM Dashboard.
```

### Step 2: Behavior, hard-code the states
```
Apply the following logic constraints to the {{screen}} flow:
- Add shimmering and loading state when for all the screens to handle when data loading takes time 
- Add stepper number 1 , 2 , 3 etc 
- If no team member is invited, provide the option to invite in the workspace screen
- Workspace should show the list of tasks assigned and options to create new tasks and also view the teams tasks and who is assigned
- when there are error gracefully handle the error states like no data returned or wrong email address 

Maintain the same design language throughout and tether all behavior strictly to these rules.
```

### Step 3: Refine, one surgical polish
```
The Acme onboarding flow needs a professional  Asana-style polish.
1. Start by listing the 3 biggest gaps in typography and spacing compared to Asana in the Acme flow.
2. Once you've identified those, fix them in the Acme flow 

Don't change anything else in the project or touch the underlying logic.
```

## Reusable techniques learned

- Defining the sequence, behaviour and refine helps to structure the build

## What broke (and the fix)

_Where a single mega-prompt failed and chaining fixed it._

Prompt chaining hardened the flow 

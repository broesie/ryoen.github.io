---
layout: tutorial
title: Control Home-Assistant by voice (in any language)
comments: true
---
This is a tutorial how you can control your devices by using your voice in your own language....
All this can be also triggered by Google Now.

## Requirements:
- An android phone
- Tasker
- AutoVoice

## Setup Tasker:

### Step 1: Check your global variables

Be sure before you start, that you have setup your tasker correctly, by creating global variables. See also how to [configure Tasker for Home Assistant](https://broesie.github.io/tutorials/hass/tasker_integration/set_global_vars)

### Step 2: Check your tasks to control your devices
In previous tutorials, was explained how to create seperate tasks to control your devices in Tasker.
Be sure that you have created such tasks. It is explained [here](https://broesie.github.io/tutorials/hass/tasker_integration/control_hass_devices)

### Step 3: Configure your autovoice plugin:

- **Open de app: AutoVoice**
- **Activate Google Now integration**, if you want integration with Google Now
- Set your **language: General settings > Default Recognize Settings > Language Settings > Language (Also Language Code)**

**Don't forget to enable in your android settings, the accessibility for Autovoice**

### Step 4: Create your profile and task

#### Example 1: task to turn off/turn on task 

Let's assume you already created a task called **HA - LivingTop On** and **HA - LivingTop Off** in step 2.

##### Your profile:

- Create a **new profile**
  - Choose **Event > Plugin > Autovoice Recognize**
  - Choose **Advanced**
  - **Checkmark Regex**
  - As **command filter** use: ```turn (?<state>.+) my (?<device>.+)```

##### Your task:

Work with if-else function

I prefer to use just the If statement instead of the if-else statement, because I can collapse my code...

#### Example 1: Turn Living Top Lights On or Off

- **If %state ~R on AND %device ~R living lights** (turn on living lights), use match regex!
  - **Perform Task: HA - LivingTop On** (See step 2)
- **End if**

- **If %state ~R off AND %device ~R living lights** (turn off living lights), use match regex!
  - **Perform Task: HA - LivingTop Off** (See step 2)
- **End if**

Also it can be shorter, if you want: just by using eg: **Perform Task: HA - LivingTop Off** and checkmark the if-statement, and put there: **If %state ~R off AND %device ~R living lights**. So in that case, you only use 1 action, instead of 3...

#### Example 2: Dim lights task

##### Your profile:

- Create a **new profile**
  - **Choose Event > Plugin > Autovoice Recognize**
  - **Choose Advanced**
  - **Checkmark Regex**
  - As **command filter** use: ```dim (?<device>.+) to (?<level>.+)%```

##### Your task:

- ```Variable set: %percent to round(255/100*%level)``` **(Enable Do Maths!)**
- **If %device ~R living lights** (use match regex!)
  - **Variable set: %service** to **light/turn_on**
  - **HTTP Post:**
    - Service port: **%HASS_SERVICE%service%HASS_PSW**
    - In data / file: **{"entity_id":"%HASS_TOPLIGHT","brightness":"%percent"}**
    - content/type: **application/JSON**
- **End if**

#### Other Commands

Above a little example to turn on or turn off things, but you can create also other profiles likes:

- ```dim (?<device>.+) to (?<percentage>.+)%```
- ```activate (?<scene>.+) mode```
- ....

The beauty of this, is that I can use Google now, so I can use Google AI to recognize my speech, and when I say a command by saying OK Google, it will also send it to my tasker autovoice...

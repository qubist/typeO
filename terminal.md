Using Terminal on reMarkable
============================

Prompt
------

### Add battery percentage to the prompt

```
# Show battery percentage in prompt
function battery {
    rot power get batteryLevel
}
PS1='\u@\H:\w ($(battery)%) $ '
```

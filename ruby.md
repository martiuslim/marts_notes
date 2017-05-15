# Ruby

## String to List

```
poem = "My toast has flown from my hand
    And my toast has gone to the moon.
    But when I saw it on television,
    Planting our flag on Halley's comet,
    More still did I want to eat it."
```

To convert this paragraph of text into a list of lines, use `lines.to_a`. 

Now poem will become
```
["My honeydew has flown from my hand\", 
"And my toast has gone to the moon.\", 
"But when I saw it on television,\", 
"Planting our flag on Halley's comet,\", 
"More still did I want to eat it.\"]
```

To reverse use `.reverse` and to join the lines back into a paragraph use `.join`.

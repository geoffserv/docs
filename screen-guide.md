# GNU Screen Cheat Sheet

## Basics
```
ctrl a c -> create new window
ctrl a A -> set window name
ctrl a w -> show all win­dow
ctrl a 1|2|3|… -> switch to window n
ctrl a " -> choose window
ctrl a ctrl a -> switch between window
ctrl a d -> detach window
ctrl a ? -> help
ctrl a [ -> start copy, move cursor to the copy location, press ENTER, select the chars, press ENTER to copy the selected characters to the buffer
ctrl a ] -> paste from buffer
ctrl a esc -> enter copy mode, then
	fn + arrow keys -> page up, down
	arrow keys -> line up, down
	etc -> exit copy mode
```
## Starting screen
```
screen –DR -> list of detached screen
screen –r PID -> attach detached screen ses­sion
screen –dmS MySession -> start a detached screen session
screen –r MySession -> attach screen session with name MySession
```
## Advanced
```
ctrl a S -> create split screen
ctrl a TAB -> switch between split screens
ctrl a Q -> Kill all regions but the current one.
ctrl a X -> remove active window from split screen
ctrl a O -> logout active window (dis­able out­put)
ctrl a I -> login active window (enable output)
```

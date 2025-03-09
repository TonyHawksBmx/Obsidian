#PANE
Create a new pane		-	CTRL + B ; 	%(horizontal),
							"(vertical)
Switch between the panes	-	CTRL + B ; 	Navigation Keys
Resize				-	CTRL + B ;	CTRL + Naviagation Keys
Give Commands			-	CTRL + B ;	:
Close				-	CTRL + B ; 	w

#WINDOW
Create New Window		-	CTRL + B ;	C
Rename Window			-	CTRL + B ;	,
Switch between Windows		-	CTRL + B ;	p (previous), n(next), w(show-windows)

#SESSION
Detach				-	CTRL + B ;	d
List				-	tmux ls
Attach				-	tmux attach -t [number]
Rename				-	tmux rename-session -t [number] [name]
New				-	tmux new -s [name]
Kill				-	tmux kill-session -t [number]


#COMMAND
split-window
vsplit-window

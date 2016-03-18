# Intro

Tmux is a terminal mertiplexer, which let you easily swich between several programs in one terminal. You can detach them (but still run in the background) and reattach them to a different terminals. This makes tmux a really great tool when you are working with a remote machine. It's similar to GNU screen, and tmux and screen serves the same purpose. 

# Commands

####prefix == 'control + b"


## Session 

-----

new session

~~~
$ tmux new -s session_name
~~~

-----

attach a session

~~~
$ tmux attach -t session_name
$ tmux a -t session_name
or
$ tmux a
~~~

-----

switching to pre-existing session. 

~~~
$ tmux switch -t session_name
~~~

-----

list existing sessions

~~~
$ tmux list-sessions
$ tmux ls
~~~

-----

detach the currently attached session

~~~
$ tmux detach (prefix + d)
~~~

-----


## Windows 


create a new window

~~~
$ tmux new-window (prefix + c)
~~~

-----

move to the window based on index

~~~
$ tmux select-window -t :0-9 (prefix + 0-9)
~~~

-----

rename the current window

~~~
$ tmux rename-window (prefix + ,)
~~~

----

splits the window into two horizontal panes

~~~
$ tmux split-window (prefix + ")
~~~

----

`splits the window into two vertical panes

~~~
$ tmux split-window -h (prefix + %)
~~~

## Panes

swaps pane with another in the specified direction

~~~
$ tmux swap-pane -[UDLR] (prefix + { or })
~~~

----

selects the next pane in the specified direction
~~~
$ tmux select-pane -[UDLR]
~~~

----

selects the next pane in numerical order

~~~
$ tmux select-pane -t :.+
~~~

-----
# wofi-pass
```
Usage: wofi-pass [options]
	-a, --autotype		autotype whatever entry is chosen
	-c, --copy [cmd]	copy to clipboard. Defaults to wl-copy if no cmd is given.
	-f, --fileisuser	use the name of the password file as username
	-h, --help		show this help message
	-s, --squash		don't show field choice if password file only contains password
	-t, --type [cmd]	type the selection instead of copying to clipboard.
				Defaults to wtype if no cmd is given.
```

Since `wofi` isn't a drop-in replacement for `rofi`, I couldn't use 
[rofi-pass](https://github.com/carnager/rofi-pass) anymore. So, I just made a 
version of `passmenu` that accomplishes everything I needed from `rofi-pass`. 

## What does it do?
This script uses [wofi](https://hg.sr.ht/~scoopta/wofi) and 
[wtype](https://github.com/atx/wtype) to provide a completely 
Wayland-native way to conveniently use [pass](https://www.passwordstore.org/). 
It provides the same search that `passmenu` does, but shows a second dialogue 
that lets the user choose which field to copy/print.

It also assumes that [pass-otp](https://github.com/tadfisher/pass-otp) is 
installed if an `otpauth://...` string is present in a password file.

The script assumes password files are formatted like the following:
```
Th3Gr3at3stPassw0rd
username: JohnDoe
email: john@example.com
otpauth://totp/example?secret=ABCDCBABCDCBABCD
pin: 1234
```
Note that the password is **ALWAYS** on the first line.

The `-s | --squash` flag tells `wofi-pass` to "intelligently" skip 
the field choice dialogue when there is only a password in the file.

The `-t | --type` flag tells `wofi-pass` to type the choice instead of copying 
to clipboard. This also enables the autotype choice which types 
`username :tab password`.

## Configuration
wofi-pass may read its configuration values from different locations in the following order:
* `$XDG_CONFIG_HOME/wofi-pass/config` (defaulting to `$HOME/.config/wofi-pass/config`)
* `/etc/wofi-pass.conf`

wofi-pass only loads the first existing file. In case no config file exists, wofi-pass uses its internal default values.

For an example configuration please take a look at the included `config.example` file.

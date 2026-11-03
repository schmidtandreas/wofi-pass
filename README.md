![testing](https://github.com/schmidtandreas/wofi-pass/actions/workflows/testing.yml/badge.svg)

# wofi-pass
```
Usage: wofi-pass [options]
	-a, --autotype[=name]	autotype using format 0-9 or an alias (default: 0)
	-c, --copy [cmd]	copy to clipboard. Defaults to wl-copy if no cmd is given.
	-f, --fileisuser	use the name of the password file as username
	-h, --help		show this help message
	-i, --insensitive	enable case insensitive search
	-s, --squash		don't show field choice if password file only contains password
	-t, --type [cmd]	type the selection instead of copying to clipboard.
				Defaults to wtype if no cmd is given.
```

Since `wofi` isn't a drop-in replacement for `rofi`,
I couldn't use [rofi-pass](https://github.com/carnager/rofi-pass) anymore.
So, [Joel Beckmeyer](https://github.com/TinfoilSubmarine) and me just made
a version of `passmenu` that accomplishes everything we needed from `rofi-pass`. 
However, suggestions are always welcome best in the form of a PR.

## What does it do?
This script uses [wofi](https://hg.sr.ht/~scoopta/wofi),
[wcopy](https://github.com/bugaevc/wl-clipboard) and 
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

The `-t | --type` flag tells `wofi-pass` to type the choice
instead of copying to clipboard.

An entry can be marked with `autotype_always` and
is thus always automatically output as autotype.

## Configuration

`wofi-pass` can read its configuration values from various locations
in the following order:
* `${WOFI_PASS_CONFIG}`
* `${XDG_CONFIG_HOME}/wofi-pass/config`
* `/etc/wofi-pass.conf`

If `${XDG_CONFIG_HOME}` environment variable is not set,
`${HOME}/.config` will be used.

`wofi-pass` loads only the first existing file.
If no configuration file exists, `wofi-pass` uses its internal defaults.

An example configuration file can be found in the supplied `wofi-pass.conf` file.

### Multiple autotype formats

Define up to ten formats: `WOFI_PASS_AUTOTYPE` (format 0) and
`WOFI_PASS_AUTOTYPE_1` through `WOFI_PASS_AUTOTYPE_9`. For example:

```bash
WOFI_PASS_AUTOTYPE=":username :tab :password"
WOFI_PASS_AUTOTYPE_1=":password"
WOFI_PASS_AUTOTYPE_2=":username :tab :password :tab :otp"
```

Select one format per invocation with `wofi-pass -t -a1` or
`wofi-pass -t --autotype=2`. The number must be attached to `-a`, or
provided with `=` for `--autotype`. Plain `-a` and `-a0` use the default.
Repeated autotype options and unknown formats are rejected.
Without `--autotype`, type mode (`-t`) also lists numbered formats in the
field-selection menu as `autotype 1` through `autotype 9`, where a username is
available. Numbered formats matching `WOFI_PASS_AUTOTYPE` are hidden. Select a numbered
entry to use it; `autotype default` and `Alt-a` use format 0.
Format 0 has the fixed, reserved alias `default`, selectable with
`--autotype=default` or `-adefault`.
Unset or empty numbered formats inherit `WOFI_PASS_AUTOTYPE` after the
configuration file is loaded. Override each desired format in the
configuration file. An `autotype` field in the password entry overrides the
selected format.

Set `WOFI_PASS_AUTOTYPE_1_ALIAS` through `WOFI_PASS_AUTOTYPE_9_ALIAS` to
optionally name a numbered format:

```bash
WOFI_PASS_AUTOTYPE_1=":password"
WOFI_PASS_AUTOTYPE_1_ALIAS="work-login"
```

The menu then shows `autotype work-login` instead of `autotype 1`.
Use `wofi-pass -t --autotype=work-login` or `wofi-pass -t -awork-login`
to select it directly. Numeric selection (`-a1`) still works.
Aliases must be unique, nonnumeric, and contain no whitespace; matching is
case-sensitive. An unset or empty alias keeps the numeric label. Formats
matching the default remain hidden even if they have an alias.

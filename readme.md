# Elder Kings Optimized

This mod speeds up health and harm events evaluation, by caching result of `ek_human_age_equivalent_calc` in a variable. As a result, the subroutine is only called once per year for most characters.

## Technical requirements

* Crusader Kings version 1.19.0.6.
* Elder Kings modification version 0.19.0.1.

Should also work for some previous versions, like 0.18.*.

## Motivation

CK3 runs most scripts in single thread. Game would lag if there are too many characters with too many scripts. Or at least spend more electric power.

Every year, every character in the world might get harm events thru `yearly_health_pulse`. To decide which events would occur, trigger/weight calculation is performed, for `health.7200` event, then for `health.7300`, then for other events.  

At trigger/weight evaluation, `health.7200` event usually calls `ek_human_age_equivalent_calc` 6 times for most characters above 18yo, and 16 times for characters of age 50 or more. That is because the event trigger evaluates `ek_human_age_equivalent` three times, and `ek_human_age_equivalent` calls `ek_human_age_equivalent_calc` twice. For most 50yo, weight calculation occurs, that calls `ek_human_age_equivalent` at least 7 times.

Other events and effects call the subroutine, too. Matchmaking subroutines surely do.
I did not count exactly, but I believe that `ek_human_age_equivalent_calc` is called dozens — if not hundreds — of times per adult character per year, on average.

In my games I increase birth-rate (for me and all other characters), and thus care a lot about scripts that evaluate on every character in the world.

## Installation

1. Put all files except `readme.md` into `mod` directory, where you put `elder-kings-ck3.mod` file and `elder-kings-ck3` directory.
2. Activate via launcher called `dowser.exe`.

For more details on mod installation, see [wiki](https://ck3.paradoxwikis.com/Modding#Installing_mods_manually).

## Load order

1. Elder Kings
2. [Elder Kings Fixes and Tweaks](https://github.com/krisk0/ek_fixes_and_tweaks) — optional, recommended.
3. Elder Kings Optimized — this mod.

## Question to Elder Kings team

In versions 0.19.0.1 and 0.18.0.2, `ek_human_age_equivalent` subroutine calls `ek_human_age_equivalent_calc` twice for 19yo or older characters:
```
ek_human_age_equivalent = { # returns the scope's age in human lifespan
	if = {
		limit = { age > 18 }
		if = {
			limit = { ek_human_age_equivalent_calc > 18 }
			value = ek_human_age_equivalent_calc
		}
		else = {
			value = 18
		}
	}
	else = {
		value = age
	}
}
```

The subroutine can be optimized to only call `ek_human_age_equivalent_calc` once:
```
ek_human_age_equivalent = {
	if = {
		limit = { age > 18 }
		value = ek_human_age_equivalent_calc
		min = 18
	}
	else = {
		value = age
	}
}
```

The question is: is that intentional sabotage? If not can you please fix your code? And do similar change in `add_magicka` effect?

## My mods

List of my modifications for CK3, and my load order is [here](https://gist.github.com/krisk0/3c51136a877afd606c184a575400922f).

# Zenonia 5

This is a hack for the game Zenonia 5 v1.2.1 with the following features:

- Get every item (also zen items)
- Item count wont decrease
- Refine always success
- You can sell everything
- Buying price set to 0
- Selling price increased
- Fast leveling (EXP * 5)
- Double HP
- SP increased
- No Cooldown
- Can move everything to your bank

# Getting Started
- Download Zenonia 5 version 1.2.1 apk
- Delete /lib/armeabi-v7a/
- Extract /lib/armeabi/libgameDSO.so
- Dump the lib with xxd in plain style
    - $ xxd -p libgameDSO.so > libgameDSO.hex
- Patch the dump
    - $ patch libgameDSO.hex libgameDSO.patch
- Reverse the patched file into binary with xxd
    - $ xxd -r -p libgameDSO.hex libgameDSO.so
- Move the patched lib to /lib/armeabi/
- Sign the apk

# Additional info for reverse engineering
If you want to disassemble the lib, you can use objdump,
but you will need to build it yourself and enable the arm
architecture.

## Build
- $ git clone git://sourceware.org/git/binutils-gdb.git 
- $ cd binutils-gdb
- $ ./configure --enable-targets=arm-eabi
- $ make

## Use
- $ ./binutils/objdump -m arm -D libgameDSO.so > libgameDSO.dump

## Example
If you want to change your startup item then you can modify the
"_ZN10CMvItemMgr17InitNewPlayerItemEP9CMvPlayer17EnumCharClassType" function
and set the id of the item you want into register 1.

You can set the value from address 11cfca to 11cfd0.

Here you see the diff of the original libgameDSO dump and the modded one, which gives you
the "Berserker Avatar 1" at the beginning of the game.

```
338970,338973c338971,338974
<   11cfca:	18e1      	adds	r1, r4, r3
<   11cfcc:	0409      	lsls	r1, r1, #16
<   11cfce:	1409      	asrs	r1, r1, #16
<   11cfd0:	1c13      	adds	r3, r2, #0
---
>   11cfca:	241e      	movs	r4, #30
>   11cfcc:	211e      	movs	r1, #30
>   11cfce:	4361      	muls	r1, r4
>   11cfd0:	3100      	adds	r1, #
```

This would multiply 30 * 30 and add 0 into r1, so the id 900 would be set,
which is "Berserker Avatar 1".

```1E * 1E + 00 = 30 * 30 + 0 = 900 = "Berserker Avatar 1"```

You can find more item ids in the "item.list" file.
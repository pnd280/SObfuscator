# SObfuscator
AutoIt3 Obfuscator written in AutoIt3

This is an **AutoIt3** code obfuscator written in **AutoIt3**, is designed and developed since March 2022. The one and only purpose of this project is to **prevent** your AutoIt3 original scripts from being pirated or analysed.

0.0.1 Beta is available for download.

## Motivations
Throughout years of developing and distributing softwares/scripts with AutoIt3 under both commercial and private closed-source format. I and maybe all developers are not wish their intellectual properties being pirated or modified without consent, specifically for AutoIt3 where it's so easy to extract the original script from compiled products. I, myself have demanded a real working Obfuscator for so so long, as (almost all) public released Obfuscators are not working properly, poorly made, or were deprecated for unknown reasons, afaik... I tried to ask and do searches in many AutoIt communities but found no hopes but meanwhile i learned lots of thing that helped and motivated me making this project. Welp, "fine, i'll do it myself".

## Todos
In priority order
- [x] ~~Stripper module: strip `;comments`, merge `#includes`, strip unused `Func functions(...) ... EndFunc`.~~ **Au3Stripper** 
- [x] ~~Beautifier module~~ **Tidy**
- [ ] Finish [Usage](#usage) section.
- [ ] Refactor.
- [ ] Stop bullsh1ting and upload the real codes.
- [ ] Build own function rename module.

## Highlights
- [x] Able to parse callback functions. ([Limitations](#limitations)#1)
- [x] Supports unicode.
- [x] Supports COM dot statements. ([Limitations](#limitations)#2)
- [x] Automatically split very long line to smaller parts (>4095 characters).
- [x] Integrates countermeasures against analysing & deobfuscating.
	  Uncompiled obfuscated scripts might not be able to properly execute at analyser's environment.

## FAQ
- Q: Is this project still being maintained?
  A: Yes. (April 21 2022)
- Q: Is source code available?
  A: Not yet ready for public usage.

<!-- - This is the naviagation to [**Usage**](#usage) section for those who don't want to read and only here to use the Obfuscator. -->

## Overview

`original_script.au3` => Obfuscation Engine ([Parser](#parser) + Obfuscation Modules/[Strategies](#obfuscation-strategies)) => `obfuscated_script.au3`

## Parser
Nothing here yet.

## Obfuscatable elements

For now the obfuscator only supports:
- `$variables`
- `'string literals'`
- `Numbers`, `0xHexNumbers`
- `@macros`
- `True`, `False`, `Default`
- Native & User defined `functions(...)`
- `.dotStatements`

## Obfuscation strategies
These strategies are not in any particular order and only performed in the obfuscated script, original one is untouched.

#### Rename variables


<table>
<thead>
<tr>
<td class="table">

```autoit
Global $iVar = 12345
Local $sVariable = 'SObfuscator'
Dim $aArray = [1, 2, 3]
```

</td>
<td class="table">==></td>
<td class="table">

```autoit
Global $9c21n1bc = 12345
Local $qwoishfx = 'SObfuscator'
Dim $_wdx8_10 = [1, 2, 3]
```

</td>
</tr>
</thead>
</table>

Of course, all references to the renamed variables are also replaced.

#### Rename user defined functions


<table>
<thead>
<tr>
<td class="table">

```autoit
Call('a')
Call(b)

Func a()

EndFunc

Func b()

EndFunc

Func c()

EndFunc
```

</td>
<td class="table">==></td>
<td class="table">

```autoit
Call('X01h16ba')
Call(_w1Ukpm_)

Func X01h16ba()

EndFunc

Func _w1Ukpm_()

EndFunc

Func Onw620zm()

EndFunc
```

</td>
</tr>
</thead>
</table>

All references to the renamed variables are also replaced, including callbacks.
See also: [Replace used functions with variables](#replace-used-functions-with-variables).

#### Replace encrypted string literals with variables


<table>
<thead>
<tr>
<td class="table">

```autoit
Local $sString1 = 'This'
Local $sString2 = "is"
Local $sString3 = '''SObfuscator''' ; apostrophe escape

a('b')

Func a($sCallback)
	Call($sCallback)
EndFunc

Func b()

EndFunc
```

</td>
<td class="table">==></td>
<td class="table">

```autoit
#Region This will be hidden
Global $1rj28nrx = BinaryToString('0x54686973', 4) ; This
Global $a42k1x4m = BinaryToString('0x6973', 4) ; is
Global $_bj_291j = BinaryToString('0x27534F626675736361746F7227', 4) ; 'SObfuscator'
Global $jjjjjjjj = BinaryToString('0x54686973', 4) ; b
#EndRegion This will be hidden

Local $sString1 = $1rj28nrx 
Local $sString2 = $a42k1x4m 
Local $sString3 = $_bj_291j 

a($jjjjjjjj)

Func a($sCallback)
	Call($sCallback)
EndFunc

Func b()

EndFunc
```

</td>
</tr>
</thead>
</table>

Support quotation and apostrophe escapes.
Use slightly modified version of native **StringToBinary** for encryption.
See also: [Replace used functions with variables](#replace-used-functions-with-variables).

#### Replace encrypted numbers with variables


<table>
<thead>
<tr>
<td class="table">

```autoit
Local $iNum = 66
Local $iHexNum = 0x8
Local $aiArray[3] = [0, 1, 2]

a(1, 0x2)

Func a($iParam1, $iParam2)
	
EndFunc
```

</td>
<td class="table">==></td>
<td class="table">

```autoit
#Region This will be hidden
Global $1rj28nrx = Number(BinaryToString('0x3636', 4))
Global $a42k1x4m = Number(BinaryToString('0x38', 4))
Global $q98wf9q_ = Number(BinaryToString('0x33', 4))
Global $0vais9us = Number(BinaryToString('0x0', 4))
Global $9f0x_qfw9 = Number(BinaryToString('0x1', 4))
Global $jjjjjjj3 = Number(BinaryToString('0x2', 4))
Global $w8fqyxn = Number(BinaryToString('0x1', 4))
Global $__xc271n = Number(BinaryToString('0x2', 4))
#EndRegion This will be hidden

Local $iNum = $1rj28nrx
Local $iHexNum = $a42k1x4m
Local $aiArray[$q98wf9q_] = [$0vais9us, $9f0x_qfw9, $jjjjjjj3]

a($w8fqyxn, $__xc271n)

Func a($iParam1, $iParam2)
	
EndFunc
```

</td>
</tr>
</thead>
</table>

Hex numbers are converted and treated as normal number format.


#### Replace encrypted macros and keywords with variables


<table>
<thead>
<tr>
<td class="table">

```autoit
Local $iYear = @YEAR
Local $sString = 'abc' & @CRLF & 'xyz'

If (1 == True) Then
	Local $bBool = False
EndIf

Func a($vParam = Default)

EndFunc
```

</td>
<td class="table">==></td>
<td class="table">

```autoit
#Region This will be hidden
Global $1rj28nrx = Execute(BinaryToString(0x4059454152, 4)) ; @YEAR
Global $a42k1x4m = Execute(BinaryToString(0x4043524C46, 4)) ; @CRLF
Global $_bj_291j = Execute(BinaryToString(0x54727565, 4)) ; True
Global $0qw9fujm = Execute(BinaryToString(0x46616C7365, 4)) ; False
Global $jjjjjjjj = Execute(BinaryToString(0x44656661756C74, 4)) ; Default
#EndRegion This will be hidden

Local $iYear = $1rj28nrx
Local $sString = 'abc' & $a42k1x4m & 'xyz'

If (1 == $_bj_291j) Then
	Local $bBool = $0qw9fujm
EndIf

Func a($vParam = $jjjjjjjj)

EndFunc
```

</td>
</tr>
</thead>
</table>

All native `@macros` and only keywords `True`, `False`, `Default` are replaced.


#### Replace used functions with variables

<table>
<thead>
<tr>
<td class="table">

```autoit
Call('a')
Call(b)

b()

Func a()

EndFunc

Func b()

EndFunc
```

</td>
<td class="table">==></td>
<td class="table">

```autoit
#Region This will be hidden
Global $hc98211q = Execute(BinaryToString('0x43616C6C', 4)) ; Call
Global $_wqj281n = Execute(BinaryToString('0x61', 4)) ; a
Global $xu8921n8 = Execute(BinaryToString('0x62', 4)) ; b
#EndRegion This will be hidden

$hc98211q('a')
$hc98211q(b)

$xu8921n8()

Func a()

EndFunc

Func b()

EndFunc
```

</td>
</tr>
</thead>
</table>

Native functions that use function name as a parameter (only as *single string literal/user function handle*) are automatically detected and replaced, with user defined functions you have to use *user function handles* (function name without quotations, Eg: `MyFunc(a) instead of MyFunc('a')`). Otherwise the engine would not be able to parse them.

#### Shuffle orders of function definitions


<table>
<thead>
<tr>
<td class="table">

```autoit
Call('a')
Call(b)

Func a()

EndFunc

Func b()

EndFunc

Func c()

EndFunc
```

</td>
<td class="table">==></td>
<td class="table">

```autoit
Func b()

EndFunc

Call('a')
Call(b)

Func c()

EndFunc

Func a()

EndFunc
```

</td>
</tr>
</thead>
</table>

Order of function's definitions is not important.

#### Hide dot statements
<table>
<thead>
<tr>
<td class="table">

```autoit
Local $var = someThing().find
```

</td>
<td class="table">==></td>
<td class="table">

```autoit
Local $var = Execute('someThing().find')
```

</td>
</tr>
</thead>
</table>

The statement inside **Execute** will be obfuscated as a normal string.
Currently, left-sided dot statement of assign statements can NOT be pass to **Execute**.

#### Hide variables

Declarations of variable which is generated by replacement strategies are not explicitly written in the script (you won't find the line `$var = value`), they are implicit and hidden in an encrypted data string (called as **TBL**) which can be stored inside the script itself or somewhere else.

This is done by:
- Manipulating AutoIt3 native **Execute**, **Assign** functions.
- Adding `#AutoIt3Wrapper_Run_AU3Check=n` at the beginning of the obfuscated script.

See also: [Demo](#demo)


## Anti deobfuscate/debug/analyse strategies

#### Using weird variable format
This format is being use all over the script to anti common RegEx patterns that analyser use for deobfuscation.

#### Detect debug programs
Nothing here yet.

#### Detect decompilation
Nothing here yet.

#### Store TBL data online
Nothing here yet. This is lame but i will somehow try to implement this in the future.

## Demo

Original

```autoit
MsgBox(4096, "SObfuscator", "Hello Word" & @CRLF & "Únìcôđẽ")
```

Obfuscated

```autoit

#AutoIt3Wrapper_Run_AU3Check=n
_uB5WDrN()
Func ofS7HJLS($5XrMdukn, $LwGzOvbl)
	$5XrMdukn = $hVabmUwU($5XrMdukn, 0x4)
	If Int($LwGzOvbl) == 0x1 Then $5XrMdukn = $Kpzd4H5Y($5XrMdukn)
	If Int($LwGzOvbl) == 0x2 Then $5XrMdukn = $M1Pyimpj($5XrMdukn)
	Return $5XrMdukn
EndFunc
Func _uB5WDrN()
	Global $Kpzd4H5Y = Execute, $hVabmUwU = BinaryToString, $M1Pyimpj = Number, $TiHuoFF3 = StringSplit, $vLv29AGK = Assign, $Wad2bhNh = InetRead, $Fqlk_2Qb = StringIsSpace
	Global $MM_weRNA = GWOBPyBJ, $PgHG3NY3 = ofS7HJLS, $cogd1gcd = @CRLF
	Global $BBSko5FC = LPvNNDSF(), $BBSko5FC = D1PDMdt2()
EndFunc
Func D1PDMdt2()
	Global $OEjUsZAX = $Kpzd4H5Y($2GcsNrY5[1]), $XYQgRGwg = $Kpzd4H5Y($2GcsNrY5[2]), $5LMn25gy = $Kpzd4H5Y($2GcsNrY5[3]), $VqHOidRn = $Kpzd4H5Y($2GcsNrY5[4]), $LwGzOvbl = $Kpzd4H5Y($2GcsNrY5[5])
	Global $_ = $Kpzd4H5Y
	$_ = ($_) (Chr(Random(64 + SRandom(-2139808481), 122, 1)) & Chr(Random(65, 122, 1)) & Chr(Random(65, 122, 1)) & Chr(Random(65, 122, 1)) & Chr(Random(65 + SRandom(-2139808481), 122, 1)) & Chr(SRandom(-2138722446) - 1 + Random(65, 122, 1)) & Chr(Random(65, 122, 1)) & Chr(Random(65, 122, 1)) & Chr(Random(65, 122, 1))) ("{EE09B103-97E0-11CF-978F-00A02463E06F}")
	$_(-1) = $_
	For $9BmFpU6C = 1 To $5LMn25gy[0]
		$_(Number($5LMn25gy[$9BmFpU6C])) = $PgHG3NY3($VqHOidRn[$9BmFpU6C], $LwGzOvbl[$9BmFpU6C])
	Next
EndFunc
Func LPvNNDSF()
	Global $acH9wcDQ = ''
	$acH9wcDQ &= "3130303030393934342C3130303030393934352C3130303030393934362C3130303030393934372C3130303030393934382C3130303030393934390D0A3078353334463632363637353733363336313734364637322C307834383635364336433646323035373646373236342C307843333941364543334143363343334234433439314531424142442C3078343034333532344334362C307833343330333933362C30783444373336373432364637380D0A302C302C302C302C322C31"
	If ($Fqlk_2Qb($acH9wcDQ)) Then Exit
	Local $Q3UhaekL = ''
	$Q3UhaekL &= '$MM_weRNA($acH9wcDQ)' & $cogd1gcd
	$Q3UhaekL &= '$TiHuoFF3($OEjUsZAX, $cogd1gcd, 1)' & $cogd1gcd
	$Q3UhaekL &= '$TiHuoFF3($XYQgRGwg[1], "," , 1)' & $cogd1gcd
	$Q3UhaekL &= '$TiHuoFF3($XYQgRGwg[2], "," , 1)' & $cogd1gcd
	$Q3UhaekL &= '$TiHuoFF3($XYQgRGwg[3], "," , 1)' & $cogd1gcd
	$Q3UhaekL &= '$vLv29AGK($5LMn25gy[$9BmFpU6C], $PgHG3NY3($VqHOidRn[$9BmFpU6C], $LwGzOvbl[$9BmFpU6C]), 2)' & $cogd1gcd
	Global $2GcsNrY5 = $TiHuoFF3($Q3UhaekL, $cogd1gcd, 0x1)
EndFunc
Func GWOBPyBJ($lK7rchip)
	Return $hVabmUwU('0x' & $lK7rchip, 0x4)
EndFunc

(($_((((((((74-(-21))+83)+(-64))+(-19))*1)+(-42))-54)))(((((((100082736/(-1))+100000429)*(-1))-100016939)-100088808)+300033389)))(($_(((((((-58)+(-82))-(-57))+(-24))+(-94))+200)))(((((100015601-100011732)+100020840)+99950681)+(-99965442))), ($_(((((-82)+23)+47)+11)))(((((((100063169+99962148)+100095353)-100070019)-99939314)+100074522)-100175915)), ($_(((((57-(-42))+(-90))-47)-(-37))))((((99929405+99963748)-99947982)+64774)) & $Kpzd4H5Y(($_((((((((-93)-67)-29)+21)+89)*1)-(-78))))(((((100030969-99950097)-99994796)-99967134)+299891005))) & ($_(((((((23+(-56))+8)-9)*(-1))-20)+(-15))))((((((99976969-100024248)-100095157)/1)-99988858)+300141240)))
```
Almost all the lines are written in abnormal format, it is very hard to figured out what is what.

This line
```autoit
MsgBox(4096, "SObfuscator", "Hello Word" & @CRLF & "Únìcôđẽ")
```
is equivalent to
```autoit
(($_((((((((74-(-21))+83)+(-64))+(-19))*1)+(-42))-54)))(((((((100082736/(-1))+100000429)*(-1))-100016939)-100088808)+300033389)))(($_(((((((-58)+(-82))-(-57))+(-24))+(-94))+200)))(((((100015601-100011732)+100020840)+99950681)+(-99965442))), ($_(((((-82)+23)+47)+11)))(((((((100063169+99962148)+100095353)-100070019)-99939314)+100074522)-100175915)), ($_(((((57-(-42))+(-90))-47)-(-37))))((((99929405+99963748)-99947982)+64774)) & $Kpzd4H5Y(($_((((((((-93)-67)-29)+21)+89)*1)-(-78))))(((((100030969-99950097)-99994796)-99967134)+299891005))) & ($_(((((((23+(-56))+8)-9)*(-1))-20)+(-15))))((((((99976969-100024248)-100095157)/1)-99988858)+300141240)))
```

As for the demo, i intentionally point out this line, but without reading to this point, will you be able understand what this line does? This is next level confusion compared to just using normal `$var($var, $var,...)` format. By skimming through the obfuscated code, your head would probably explode while finding what these weird `$_` with some numbers permutations represented for. 

## Usage
Nothing here yet.

## Limitations
1. Although i mentioned before in [Rename user defined functions](#rename-user-defined-functions), this strategy is done by using **Au3Stripper**, not the Obfuscator engine itself.
2. Can't obfuscate Object methods and properties that are being used explicitly at the left side of an assign statement. This is probably a native AutoIt3 limitation, afaik.

## Conclusions
Some might say that AutoIt3 scripts are easy to extract is a good thing in some cases where people are making malwares, malicious bots,... because viruses that were written in AutoIt3 is really popular back then, but i think this should not be an excuse. Nowadays, some AVs still tend to always red flagging AutoIt3 executables regardless of knowing what is it doing. I, myself find this approach really awkward, maybe those AVs are not capable to analyse and simply allow false positives as their very last resort.

In my perspective, the only reliable way to "protect" AutoIt3 source code is to obfuscate it, make it unreadable while maintaining functionality and performance. Obfuscation does NOT add any extra protection to the compiled executables, the word "**prevent**" only meant to make reverse engineering much more harder and nearly impossible to read/understand for those who are not familiar with the syntax. The only things that would be lost are variable, function names and the original function's definition orders.

This weakness is unavoidable unless the founders take this matter seriously and make changes, maybe not in the near future but i hope they will do eventually, considering that AutoIt3 is a great language/tool and maybe the best in some tasks that other languages may struggle.

## Acknowledgements
- [AutoIt3](https://www.autoitscript.com) for various resources:
	- [Tidy](https://www.autoitscript.com/autoit3/scite/docs/SciTE4AutoIt3/Tidy.html) & [Au3Stripper](https://www.autoitscript.com/autoit3/scite/docs/SciTE4AutoIt3/Au3Stripper.html).
	- Many pieces of code across the [forum](https://www.autoitscript.com/forum), mostly RegEx patterns.
- 721PC, trancexx for main ideas.
- VSCode & [Loganch](https://github.com/loganch/AutoIt-VSCode) for providing robust IDE.

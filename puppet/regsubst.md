###String substitute in puppet

To replace substr in a string using puppet ,we can use regsubst function.

Docs from [puppet](http://docs.puppetlabs.com/references/latest/function.html#regsubst):

Parameters (in order):
        target The string or array of strings to operate on. If an array, the replacement will be performed on each of the elements in the array, and the return value will be an array.
        regexp The regular expression matching the target string. If you want it anchored at the start and or end of the string, you must do that with ^ and $ yourself.
        replacement Replacement string. Can contain backreferences to what was matched using \0 (whole match), \1 (first set of parentheses), and so on.
        flags Optional. String of single letter flags for how the regexp is interpreted:
            E Extended regexps
            I Ignore case in regexps
            M Multiline regexps
            G Global replacement; all occurrences of the regexp in each target string will be replaced. Without this, only the first occurrence will be replaced.
        encoding Optional. How to handle multibyte characters. A single-character string with the following values:
            N None
            E EUC
            S SJIS
            U UTF-8
    Examples

Get the third octet from the node’s IP address:

    $i3 = regsubst($ipaddress,'^(\d+)\.(\d+)\.(\d+)\.(\d+)$','\3')

Put angle brackets around each octet in the node’s IP address:

    $x = regsubst($ipaddress, '([0-9]+)', '<\1>', 'G')


```
$ipaddr = "10.0.2.15"
$class_c = regsubst($ipaddr, '(.*)\..*', '\1.0')
notify { $ipaddr: } #10.0.2.15
notify { $class_c: } #10.0.2.0

$braceip = regsubst($ipaddr, '([0-9]+)', '<\1>', 'G') #<10>.<0>.<2>.<15>

$pwd = 'ad$m$abc'
$pwde = regsubst($pwd, '(\$)', '\\\1', 'G')  #ad\$m\$abc
$pwdf = regsubst($pwd, '\$', '\\$', 'G')  #ad\$m\$abc
notify { $pwde: }

$hostn = "web1"
$hostnn = regsubst($hostn, '\d+$', '')  #web
Or if you prefer to actually call out to Ruby, you can use an inline ERB template:
$hostname_without_number = inline_template('<%= @hostn.gsub(/\d+$/, "") %>') # puppet 3.8.5 need @hostn format
notify { $hostname_without_number: }
$x1 = 'des1'
$x2 = 'des2'
alert("x1= $x1")
alert("x2= $x2")
$y1 = regsubst($x1, '[12]$', {'1' => 2, '2' => 1})
$y2 = regsubst($x2, '[12]$', {'1' => 2, '2' => 1})
alert("y1= $y1")
alert("y2= $y2")



```

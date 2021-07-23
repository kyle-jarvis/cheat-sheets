<img align="right" src="https://img.shields.io/badge/bash-4EAA25?style=for-the-badge&logo=gnubash&logoColor=white" width=150>

## Reference
1. [`flow-control`](#flowcontrol)
2. [`boilerplate`](#boilerplate)
1. [`sed`](#sed)

### 1. `flow-control`
[Top](reference)

```bash
# [[ ]] vs [ ] vs (( ))
# [[ ]] has a number of enhancements over [ ] (but is less portable)
# Enhancements include combining expressions ||, &&, globbing in string comparisons, regexp conditionals:
mystring='Hello, World!'
[[ $mystring == Hello* ]] && echo "true"
# true
[[ $mystring =~ "Hello, .*" ]] && echo "true"
# true
[[ $mystring =~ "Hello$" ]] || echo "false"
# false


# Test e.g. number of arguments
[[ $? -eq 0 || $? -lt 2 || $? -gt 4 ]]

# Group commands e.g. print error message and exit, otherwise continue
RESULT=10
[[ $RESULT -lt 5 ]] || { echo "Result larger than expected"; exit 1 }


# Conditionals
ARG1=
ARG2='a'
[[ -n $ARG1 ]] && echo "true"
# true
[[ -z $ARG2 ]] && echo "true"
# true

# -f : is a file
# -d : is a directory
```

### 2. `boilerplate`
[Top](reference)

```bash
#!/bin/bash

SCRIPT_PATH=$(readlink -f $0)
SCRIPT_DIR=$(dirname $SCRIPT_PATH)

# Resolve absolute paths from relative paths
OTHER_DIR=$(readlink -f ${SCRIPT_DIR}/../SIBLING_DIR)

# Parse arguments
while [[ $# -gt 0 ]]
do
    case $1 in
        --first-thing)
        # Capture a value that we expect to follow
        THING=$2
        shift
        ;;
        --flag)
        # Trigger some actions based on a flag
        SET_SOMETHING=true
        shift
        ;;
        *) other_args+=($1)
        ;;
    esac
    shift
done

```

### 3. `heredoc usage`
[Top](reference)

```bash
#!/bin.bash
cat <<EOF > some_file.txt
first line of file
second line of file
EOF

cat ./some_file.txt
first line of file
second line of file

```

### 4. `sed`
[Top](reference)

Below are some useful one-liners for sed, the stream-editor.

```bash
#!/bin/bash

echo 'Example of a short sentence to work with.' > sed_input.txt

# Global substitution (/g)
sed -r 's/short/long/g' ./sed_input.txt
# 'Example of a long sentence to work with.'

# Substitute specific occurrence (/n)
sed -r 's/en/\(en\)/1' ./sed_input.txt
# 'Example of a long s(en)tence to work with.'

# Match groups and use them in a substitution (\1, \2, etc.)
sed -r 's/(a short)/\1er/g' ./sed_input.txt
# 'Example of a shorter sentence to work with.'

# Split a space delimited string to multiple lines
sed -r 's/ /\n/g' ./sed_input.txt | head -n 3
# 'Example
# of
# a'

# Suppress output generally with -n, add p option to print matches

cat ./multi_line_input.txt
first short sentence
this is a second, longer sentence.

sed -nr 's/short/shortest/gp' ./multi_line_input.txt
# 'first shortest sentence'

# Can be handy to extract / update version number
echo "VERSION-1.0.0-green.svg" | sed -r 's/.*-([0-9\.]*)-.*/\1/g'
# '1.0.0'
```

<img align="right" src="https://img.shields.io/badge/bash-4EAA25?style=for-the-badge&logo=gnubash&logoColor=white" width=150>

## Reference
1. [`sed`](#sed)

### 1. `sed`
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
```

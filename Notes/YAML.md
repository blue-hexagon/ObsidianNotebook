# Folded Scalar
```
name: Folded Scalar. Replaces \n with a space
command: >
    put commands here, that may
    go off screen.
```
# Multiline/Block Scalar
```
name: Multiline Scalar. Use where \n is signifact.
command: |
    command one -a arg_1
    command two -a arg_1
```
# Anchors & Aliases
Anchors and aliases in YAML provide a convenient way to reuse data and avoid duplication within a YAML document.
They are particularly useful for defining default configurations and inheriting values across multiple sections of the document.
```
defaults: &defaults
  username: guest
  password: guest

admin:
  <<: *defaults
  password: secret
```
# Inline List + Inline Map
```
colors: [red, green, blue]
person: {name: John Doe, age: 30}
```
# Matrice
Aka. a list of listss
```
matrix:
  - [1, 2, 3]
  - [4, 5, 6]
  - [7, 8, 9]
```

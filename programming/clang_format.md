# Clang-format

Format options are at https://clang.llvm.org/docs/ClangFormatStyleOptions.html

Remember the yaml needs block sections to split languages.

```yaml

---
Language: Cpp
BasedOnStyle: Google
IndentWidth: 4
# Standard covers C++11 template format differences.
Standard: Auto
UseTab: Never

```

Set global formatting options in C_Cpp.clang_format_fallbackStyle.

## Visual Studio Code formatter in Linux

The code formatting is done by clang-format. It is installed as part of the ms-vscode.cpptools extension.

```bash
~/.vscode/extensions/ms-vscode.cpptools-0.24.1/LLVM/bin/clang-format
```

Dump included styles with this command.

```bash
clang-format -style=llvm -dump-config > .clang-format
~/.vscode/extensions/ms-vscode.cpptools-0.24.1/LLVM/bin/clang-format -style=llvm -dump-config > .clang-format
~/.vscode/extensions/ms-vscode.cpptools-0.24.1/LLVM/bin/clang-format -version -> 6.0.0
```

## Visual Studio Code formatter in Windows Powershell

```text
It doesn't like the env in a plain execute statement.
$env:userprofile/.vscode/extensions/ms-vscode.cpptools-0.24.1/LLVM/bin/clang-format -h

Use:
. "$env:userprofile/.vscode/extensions/ms-vscode.cpptools-0.24.1/LLVM/bin/clang-format" -h
```

## Visual examples

* https://zed0.co.uk/clang-format-configurator/
* https://clangformat.com/

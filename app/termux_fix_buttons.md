# Termux fix button bar

```bash
# One line
mkdir $HOME/.termux/;echo "extra-keys = [['ESC','/','|','-','HOME','UP','END','PGUP'],['TAB','FN','CTRL','ALT','LEFT','DOWN','RIGHT','PGDN']]" >> $HOME/.termux/termux.properties
```

```bash
# Two line
mkdir $HOME/.termux/
echo "extra-keys = [['ESC','/','|','-','HOME','UP','END','PGUP'],['TAB','FN','CTRL','ALT','LEFT','DOWN','RIGHT','PGDN']]" >> $HOME/.termux/termux.properties
```

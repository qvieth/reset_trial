Reset trial

Remember to save your preset  

<img src='image.png'>

BTT
```sh
echo "remove ~/Library/Application\ Support/BetterTouchTool/"
rm -rf ~/Library/Application\ Support/BetterTouchTool/
echo "Done"

echo "remove /Applications/BetterTouchTool.app"
rm -rf /Applications/BetterTouchTool.app
echo "Done"

echo "kill BetterTouchTool"
kill $(ps aux | grep 'BetterTouchTool' | awk '{print $2}')
echo "Done"

echo "uninstall BetterTouchTool"
brew uninstall --cask bettertouchtool
echo "Done"

echo "install BetterTouchTool"
brew install --cask bettertouchtool
echo "Done"
```

CO
```python
#!/usr/bin/env python3
import os
import shutil
import re
import plistlib
from datetime import datetime

print("Resetting Crossover FirstRunDate...")
userPath = os.path.expanduser('~')

with open(f'{userPath}/Library/Preferences/com.codeweavers.CrossOver.plist', 'rb') as f:
    pl = plistlib.load(f)

pl['FirstRunDate'] = datetime.utcnow()

with open(f'{userPath}/Library/Preferences/com.codeweavers.CrossOver.plist', 'wb') as f:
    plistlib.dump(pl, f)

print("Resetting Crossover bottles...")
while True:
    bottle_name = input("Enter the bottle name: ")

    regfile = os.path.expanduser(f"~/Library/Application Support/CrossOver/Bottles/{bottle_name}/system.reg")
    bakfile = regfile + ".bak"

    shutil.copy2(regfile, bakfile)
    print(f"Backup created: {bakfile}")

    pattern = re.compile(r"\[Software\\\\CodeWeavers\\\\CrossOver\\\\cxoffice\] [0-9]*")

    with open(regfile, 'r') as f:
        lines = f.readlines()

    match_line_num = None
    for i, line in enumerate(lines):
        if pattern.search(line):
            match_line_num = i
            break

    if match_line_num is not None:
        print(f"Match found at line {match_line_num + 1}.")
        for line in lines[match_line_num:match_line_num + 5]:
            print(line, end='')

        resp = input("Do you want to delete these lines (delete to reset bottle)? (y/n): ").strip().lower()
        if resp == 'y':
            new_lines = lines[:match_line_num] + lines[match_line_num + 5:]
            with open(regfile, 'w') as f:
                f.writelines(new_lines)
            print("Lines deleted.")
        else:
            print("Deletion canceled.")
    else:
        print("No match found.")

    resp = input("Do you want to reset another bottle? (y/n): ").strip().lower()
    if resp != 'y':
        print("CrossOver trial reset.")
        break
```

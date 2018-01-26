### file(dir) list delete operation

```
import sys
import os
import shutil


def main():
    if len(sys.argv) == 2 and sys.argv[1]=='clean':
        #remove .pyc files
        for (dirpath, dirnames, filenames) in os.walk('.'):
            for filename in filenames:
                filepath = os.path.join(dirpath, filename)
                if filepath.endswith('.pyc'):
                    os.remove(filepath)

        #remove build dir
        if os.path.exists('build'):
            shutil.rmtree('build')

    
if __name__ == '__main__':
    print 'arguments:',sys.argv
    main()
```

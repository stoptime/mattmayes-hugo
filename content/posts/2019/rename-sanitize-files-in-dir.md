---
title: "Rename and Sanitize Files in a Directory"
date: 2019-12-14T17:56:00-06:00
tags : [ "golang", "dev"]
---

Sometimes you need to rename and "sanitize" a bunch of files in a directory. 

For me, I had to do things like:
- Remove spaces.
- Remove apostrophes, commas, etc
- In some cases, remove a trailing "-"

Seemed to be a perfect opportunity to get to know [golang](https://golang.org/) a little better.

Hat-tip to [this helpful page](https://www.devdungeon.com/content/working-files-go) on working with files in golang.

Just be sure to change the `directory` to the one you want.

```go {linenos=false}
package main

import (
	"io/ioutil"
	"log"
	"os"
	"path"
	"strings"

	"github.com/kennygrant/sanitize"
)

var (
	err       error
	directory = "./_html/"
	ext       string
	fileBase  string
	saniFile  string
)

func main() {
	files, err := ioutil.ReadDir(directory)
	if err != nil {
		log.Fatal(err)
	}

	for _, file := range files {
		ext = path.Ext(file.Name())
		fileBase = strings.TrimSuffix(file.Name(), ext)
		fileBase = strings.TrimSuffix(fileBase, "-")
		saniFile = sanitize.BaseName(fileBase) + ext
		err := os.Rename(directory+file.Name(), directory+saniFile)
		if err != nil {
			log.Fatal(err)
		}
	}
}
```

Context: https://github.com/stoptime/brew
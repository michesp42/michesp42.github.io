---
title: 'Basic Introduction to LaTeX'
date: 2022-08-04T10:54:36+08:00
tags: ['typesetting']
categories: ['tutorial']
---

{{< admonition warning >}}
TODO: Unfinished just testing deployment
{{< /admonition >}}

$\LaTeX$ is a markup language used for typesetting professional looking
documents[1] especially academic papers where you write a lot of mathematical
formulae.

## Why learn and use LaTeX?

You may ask: if word processors like MS Word exist, why go over the hassle of
learning a new tool like $\LaTeX$ to simply format text?

To boot, here are just some of the advantages I can think of. Firstly, $\LaTeX$
mostly does the typesetting for you. Since, $\LaTeX$ blah blah.

Secondly, if you are in the business of technical/academic writing, is better
mathematical formulae are better formatted and look much better in $\LaTeX$.

Thirdly, you have more control over how your document looks and organized. You
can control various aspects of your document merely by specifying certain
configurations.

Lastly, cross-referencing is easy and automatic. For example, if you have a
section, say Section 3.7, in your document that you want move to another
section and it becomes say Section 3.3. In a word processor you will have to
change all the references to section 3.7 to 3.3. In $\LaTeX$, since you
reference by some label of the section, the changes and numbering adjustments
are done automatically by $\LaTeX$.

As a bonus, $\LaTeX$ source code is just plain text with some special syntax so
easy to version control

## How to install and where to write LaTeX

If you just want to try out $\LaTeX$, a web-based service you can use is
OverLeaf so that you don't have to install anything locally. On the other hand,
if you do want to install $\LaTeX$ you may follow the instructions here.

## The LaTeX Document

### The preamble and the body of the document

A simple $\LaTeX$ source code may look like this.

```latex
\documentclass[a4paper, 11pt]{article}
\usepackage{lipsum}

\author{Juan dela Cruz}
\title{My First \LaTeX Document}
\date{\today}

\begin{document}
\maketitle

\lipsum[1-2]
\end{document}
```

If we compile this it may look like this:

{{< figure src="./ex01.jpg" title="Compilation result" >}}

## Some basic things to do in LaTeX

Somethings

## References

1. Nicola Talbot, $\LaTeX$ for Complete Novices.

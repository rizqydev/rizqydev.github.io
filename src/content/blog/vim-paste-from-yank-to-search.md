---
title: 'Vim: Paste From Yank to Search'
description: 'Brief Tutorial to Paste From Yank to Search in Vim'
pubDate: '2026-08-26'
heroImage: '/blog-placeholder-1.jpg'
categories: ['vim']
language: en
---

After pressing `/` to enter a search string, you can then use `Ctrl-R` and then type the letter representing the register that you want to use.

eg.

- First, `"Ayw` to yank a word into register A
- Then, `/ ^R A` to put the contents of register A into the search string.
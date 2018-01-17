### Preface

It is the UNIX Time-sharing System, which runs on PDP11 compute system, and was developed by Ken Thompson and Dennis Ritchie at Bell Laboratories.

There seem to be three main approaches  to learning Operating System: 

##### General principles

"general principles" wherein fundamental principles are expounded, and illustrated by references to various existing systems(most of which happen to be outside of immediate exprences).

##### Building block
"building block", wherein learners are enabled to synthesize a small scale or "toy" operating system for themselves.

##### Case study
"case study", which will study a working operating system in all aspects case by case.

### Introduction

UNIX OS source code which is permanently resident in the main memory during the operation of UNIX has the following major functions:
- intialisation
- process management
- system calls
- interrupt handling
- input/output operations
- file management

Utilities
- the "shell" (the command language interpreter)
- "/etc/init" (the terminal configuration controller)
- a number of file system management programs such as: check du rmdir chmod mkdir sync clri mkfs umount df mount update(many of functions carried out by those programs are regarded as OS funcctions in other compute systems).




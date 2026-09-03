# 📘 Article 1: Reading ls -l: Understanding File Permissions from Scratch

## Introduction

If you work with Linux, macOS, servers, Git repositories, or developer tools, you will eventually see something like this:

```
-rw-r--r--  1 alex developers 1244 Sep 3 15:05 shopify.app.toml
```

At first glance, it can look like a strange combination of letters, numbers, usernames, and dates.

What does this mean?

What is:

```
-rw-r--r--
```

Why is there a:

```
1
```

Why are there two names:

```
alex developers
```

And what does:

```
1244
```

represent?

Most importantly:

> **How does Linux use all of this information to decide whether someone can access a file?**

This article starts from zero and builds the foundation for the entire **Files, Permissions & Ownership** series.

By the end, you should be able to look at an `ls -l` output and understand what you're looking at without memorizing a string of characters.

***

## 1. Why Does Linux Care About File Permissions?

Imagine a Linux server used by several people.

It contains:

```
project/
├── index.html
├── app.js
├── config.env
└── database-backup.sql
```

Should every user on the server be able to:

* read `config.env`?
* modify `app.js`?
* delete `database-backup.sql`?
* execute a particular program?
* access the `project` directory?

Probably not.

The operating system therefore needs a way to control access to files and directories.

Linux uses several pieces of information to make these decisions.

Among the most important are:

* **file type**
* **owner**
* **group**
* **permissions**

The `ls -l` command lets us inspect much of this information.

***

## 2. Meet `ls`

Open a terminal and run:

```bash
ls
```

You might see:

```
index.html
app.js
config.env
project
```

The `ls` command means:

> **list**

It lists files and directories in the current location.

But it doesn't tell us much about each item.

We can ask for more information.

***

## 3. What Does `ls -l` Mean?

Run:

```bash
ls -l
```

You might see:

```
-rw-r--r--  1 alex developers 1244 Sep 3 15:05 shopify.app.toml
```

The `-l` option means **long listing format**.

Instead of just showing:

```
shopify.app.toml
```

Linux gives us additional information about the file.

Think of it like this:

```
ls
```

means:

> "Show me what's here."

While:

```
ls -l
```

means:

> "Show me what's here and give me useful details about each item."

***

## 4. Breaking Down the Output

Let's take this line:

```
-rw-r--r--  1 alex developers 1244 Sep 3 15:05 shopify.app.toml
```

We can divide it into several parts:

```
-rw-r--r--   1   alex   developers   1244   Sep 3 15:05   shopify.app.toml
│            │     │         │          │          │              │
│            │     │         │          │          │              └─ name
│            │     │         │          │          └──────────────── date/time
│            │     │         │          └─────────────────────────── size
│            │     │         └────────────────────────────────────── group
│            │     └──────────────────────────────────────────────── owner
│            └─────────────────────────────────────────────────────── links
└──────────────────────────────────────────────────────────────────── type + permissions
```

We'll now examine each part.

***

## 5. The First Character: File Type

Look at:

```
-rw-r--r--
^
```

The first character tells us the type of filesystem object.

A:

```
-
```

normally means:

> **regular file**

For example:

```
-rw-r--r--  ...  index.html
```

The first character is:

```
-
```

so `index.html` is a regular file.

Now compare that with a directory:

```
drwxr-xr-x  ...  project
^
```

Here the first character is:

```
d
```

which means:

> **directory**

So two very common values are:

```
-   regular file
d   directory
```

There are other types of filesystem objects, such as symbolic links, which we'll cover later.

For now, these two are enough.

***

## 6. The Nine Permission Characters

After the first character we have:

```
rw-r--r--
```

That's **nine characters**.

They are divided into three groups:

```
rw-  r--  r--
│    │    │
│    │    └── Others
│    └─────── Group
└──────────── Owner
```

This is the foundation of Unix file permissions.

Linux considers three categories:

1. **Owner**
2. **Group**
3. **Others**

Each category can have three basic permissions:

```
r
w
x
```

So conceptually we have:

```
          Owner     Group     Others
           rwx       rwx       rwx
            │         │         │
            └─────────┴─────────┘
                  permissions
```

Some permissions may be missing.

A missing permission is represented by:

```
-
```

***

## 7. Understanding `r`, `w`, and `x`

The three basic permission letters are:

| Symbol | Meaning                |
| ------ | ---------------------- |
| `r`    | Read                   |
| `w`    | Write                  |
| `x`    | Execute                |
| `-`    | Permission not granted |

So:

```
rw-
```

means:

```
read      ✓
write     ✓
execute   ✗
```

And:

```
r--
```

means:

```
read      ✓
write     ✗
execute   ✗
```

And:

```
rwx
```

means:

```
read      ✓
write     ✓
execute   ✓
```

We'll spend an entire article exploring what these permissions actually mean, especially for directories.

For now, think of them as the three fundamental permission types.

***

## 8. Owner Permissions

Look again:

```
-rw-r--r--  1 alex developers ...
 └─┬─┘
   │
   owner permissions
```

The first three permission characters belong to the **owner**:

```
rw-
```

So the owner has:

```
read    ✓
write   ✓
execute ✗
```

In other words, the owner can read and modify the file but does not have execute permission.

***

## 9. Group Permissions

The next three characters are:

```
r--
```

These belong to the file's **group**.

Our example says:

```
alex developers
```

So:

```
Owner: alex
Group: developers
```

Members of the `developers` group are evaluated against:

```
r--
```

Therefore they can read the file but cannot write to or execute it.

We'll explore users and groups in detail in Article 2.

***

## 10. Other Users

The final three permission characters are:

```
r--
```

These are the permissions for **others**.

"Others" essentially means:

> Users who are neither the file's owner nor members of the relevant group.

So our example becomes:

```
Owner:
rw-

Group:
r--

Others:
r--
```

***

## 11. Putting the Permissions Together

We can now read:

```
-rw-r--r--
```

as:

```
-    rw-    r--    r--
│     │      │      │
│     │      │      └── Others
│     │      └───────── Group
│     └──────────────── Owner
└────────────────────── Regular file
```

In plain English:

> This is a regular file. Its owner can read and write it. Members of its group can read it. Other users can also read it. No category has execute permission.

That's already much more useful than memorizing `644`.

We'll learn where `644` comes from later.

***

## 12. What Is the Owner?

Our example contains:

```
alex
```

That is the **owner** of the file.

Linux keeps track of which user owns each file.

For example:

```
Owner: alex
```

This matters because Linux needs to know which permission set to use when a user tries to access the file.

If `alex` accesses the file, Linux considers:

```
rw-
```

If another user who belongs to the file's group accesses it, Linux considers:

```
r--
```

If a user is neither the owner nor a member of that group, Linux considers:

```
r--
```

The important idea is:

> **Ownership helps Linux decide which permission category applies to a user.**

We'll build this model properly in the next article.

***

## 13. What Is the Group?

The next name is:

```
developers
```

This is the file's **group**.

A group is a collection of users.

For example, a server might have groups such as:

```
developers
designers
qa
admins
```

A file can belong to a group, allowing the same permission rules to apply to multiple users.

For example:

```
project/
```

could belong to:

```
developers
```

and the group permissions could allow developers to work with the project while restricting everyone else.

This is one of the reasons groups are so useful in multi-user systems.

***

## 14. What Is the `1`?

After the permissions we see:

```
1
```

For a regular file, this is typically the number of **hard links** pointing to the file.

For example:

```
-rw-r--r--  1 alex developers ...
             ^
             hard link count
```

Hard links are a separate filesystem concept.

You don't need to understand them to learn permissions.

For now, simply recognize that:

> **The number after the permission string is not part of the permissions.**

We'll return to links later in the broader Linux learning path.

***

## 15. What Is `1244`?

Next we have:

```
1244
```

This is the file size, measured in **bytes**.

So:

```
1244
```

means the file is 1,244 bytes in size.

A larger file might show:

```
1048576
```

which represents 1,048,576 bytes.

***

## 16. What Is the Date and Time?

Next:

```
Sep 3 15:05
```

This is the timestamp displayed by `ls`, normally representing the file's modification time.

So we can think of it as:

```
Sep 3
15:05
```

The exact display can vary depending on the operating system, locale, and how old the file is.

***

## 17. The Filename

Finally:

```
shopify.app.toml
```

is simply the name of the file.

So we've now decoded the whole line:

```
-rw-r--r--  1 alex developers 1244 Sep 3 15:05 shopify.app.toml
```

We can describe it as:

> `shopify.app.toml` is a regular file owned by `alex` and belonging to the `developers` group. The owner can read and write the file, while the group and other users can read it. The file is 1,244 bytes and was modified at the displayed date and time.

***

## 18. What Does a Directory Look Like?

Let's look at a directory:

```
drwxr-xr-x  5 alex developers 160 Sep 3 15:10 project
```

The first character is:

```
d
```

So this is a directory.

The permissions are:

```
rwxr-xr-x
```

Split them:

```
rwx  r-x  r-x
│    │    │
│    │    └── Others
│    └─────── Group
└──────────── Owner
```

So:

```
Owner → rwx
Group → r-x
Others → r-x
```

But here's an important warning:

> **`r`, `w`, and `x` have special meanings when applied to directories.**

For example, directory `x` permission is related to being able to **traverse/access** the directory.

Directory `w` permission is related to operations such as creating, deleting, and renaming entries inside it.

This is one of the most important concepts we'll cover later.

***

## 19. Don't Memorize `644` Yet

You may have seen developers write:

```bash
chmod 644 file.txt
```

or:

```bash
chmod 755 script.sh
```

Don't worry about those numbers yet.

Many developers memorize:

```
644 = normal file
755 = executable
```

without understanding why.

That's backwards.

First understand:

```
-rw-r--r--
```

Then we'll derive:

```
644
```

from it.

You'll understand where the number comes from instead of treating it as magic.

***

## 20. A Simple Mental Model

For now, remember this structure:

```
             FILE
               │
       ┌───────┴───────┐
       │               │
     Owner            Group
       │
       └──── Permissions ────┐
                             │
                    ┌────────┼────────┐
                    │        │        │
                    r        w        x
                  read     write    execute
```

And remember that permissions are evaluated for three categories:

```
Owner
Group
Others
```

So:

```
-rw-r--r--
```

really means:

```
Type:    regular file

Owner:   rw-
Group:   r--
Others:  r--
```

That's the important part.

***

## 21. A Real-World Scenario

Imagine a developer joins an existing Linux project.

They run:

```bash
ls -l
```

and see:

```
-rw-r--r--  1 root developers 1244 Sep 3 15:05 config.yml
```

They try to edit `config.yml`.

Their editor reports:

```
Permission denied
```

Instead of immediately searching for a command to copy and paste, they can now start asking:

1. What type of object is this?
2. Who owns it?
3. What group does it belong to?
4. What permissions does the owner have?
5. What permissions does the group have?
6. Which category does my user fall into?

Those questions lead to the real solution.

And that is the goal of this series:

> **Understand the system first. Use the command second.**

***

## 22. Practice: Read These Yourself

Try to interpret these:

#### Example 1

```
-rw-r--r--  1 alex developers 500 README.md
```

#### Example 2

```
-rwxr-xr-x  1 alex developers 2048 deploy.sh
```

#### Example 3

```
drwxr-xr-x  5 alex developers 160 project
```

For each one, ask:

* Is it a file or directory?
* Who owns it?
* What group does it belong to?
* What can the owner do?
* What can the group do?
* What can others do?

Don't worry about understanding everything yet.

The purpose of this exercise is to become comfortable **reading the structure**.

***

## 23. The Most Important Pattern

If you see:

```
-rwxr-x---
```

don't look at it as one long string.

Break it apart:

```
-   rwx   r-x   ---
│    │     │     │
│    │     │     └── Others
│    │     └──────── Group
│    └────────────── Owner
└─────────────────── Regular file
```

Now it becomes much easier to reason about.

The same pattern applies to:

```
-rw-r--r--
-rwxr-xr-x
-r--r-----
drwxr-xr-x
drwx------
```

The characters change, but the structure stays the same.

***

## Key Takeaways

By the end of this article, you should understand these concepts:

#### `ls`

Lists files and directories:

```bash
ls
```

#### `ls -l`

Shows detailed information:

```bash
ls -l
```

#### First character

Commonly:

```
- = regular file
d = directory
```

#### Nine permission characters

They are divided into:

```
Owner   Group   Others
 rwx     rwx     rwx
```

#### Permission letters

```
r = read
w = write
x = execute
- = not granted
```

#### A file has an owner and a group

For example:

```
alex developers
```

#### The complete line

```
-rw-r--r--  1 alex developers 1244 Sep 3 15:05 shopify.app.toml
```

contains information about:

```
file type
permissions
hard links
owner
group
size
modification time
filename
```

***

## Coming Next

In **Article 02**, we'll answer one of the most important questions in Linux:

> **Who exactly is a user, and what exactly is a group?**

We'll learn why Linux doesn't simply ask:

> "Does this person have permission?"

Instead, it asks something closer to:

> **"Who is this user, who owns this file, what groups does this user belong to, and which permission rules apply?"**

We'll use simple examples to build that model from scratch.

Once that is clear, commands like:

```bash
whoami
id
groups
chown
chgrp
```

will start making sense instead of looking like commands you simply have to memorize.

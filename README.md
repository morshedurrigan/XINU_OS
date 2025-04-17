# Xinu for Mac

A guide to running [Xinu OS](https://github.com/yutaka-m/learning-xinu) on macOS (Apple Silicon compatible).

## 📦 Prerequisites

Ensure you have [Homebrew](https://brew.sh/) installed before proceeding.

## 🛠 Installation Steps

### 1. Clone the Xinu Repository

```bash
git clone https://github.com/yutaka-m/learning-xinu.git
```

### 2. Install Required Packages

```bash
brew install qemu
brew install gcc
brew install x86_64-elf-gcc
brew install flex
brew install bison
brew install gawk
```

### 3. Find Binary Paths

Locate binaries in `/opt/homebrew/Cellar/`. Example for `flex`:

```bash
cd /opt/homebrew/Cellar/flex
cd <version>  # e.g., 2.6.4_2
cd bin
pwd  # Copy this path
```

Repeat for:
- `gcc`
- `x86_64-elf-gcc`
- `flex`
- `bison`

Also, locate the `libfl.a` file inside `flex`'s `lib` folder.

## 🧑‍💻 Configure the Project

### 4. Open Project

Open `learning-xinu-main` in VS Code and create a `tftp` folder inside it.

### 5. Edit `compile/Makefile`

Update the following:

```make
COMPILER_ROOT = /opt/homebrew/bin/x86_64-elf-
CC = ${COMPILER_ROOT}gcc
LD = ${COMPILER_ROOT}ld
```

Replace:

```make
@$(ECHOxinu00) cp xinu.elf /srv/tftp/xinu.boot
```

With:

```make
@$(ECHOxinu00) cp xinu.elf ../tftp/xinu.boot
```

Append at the bottom:

```make
run:
	make clean
	make
	qemu-system-i386 -m 16 -kernel xinu.elf -nographic -append "console=ttyS0"
	clear

kill:
	kill $(shell pgrep -f qemu-system-)
	clear
```

### 6. Edit `config/Makefile`

Set your paths accordingly:

```make
COMPILER_ROOT = /opt/homebrew/Cellar/gcc/14.2.0_1/bin/
FLEX_ROOT = /opt/homebrew/Cellar/flex/2.6.4_2/bin/
BISON_ROOT = /opt/homebrew/Cellar/bison/3.8.2/bin/

CC = ${COMPILER_ROOT}gcc-14
LEX = ${FLEX_ROOT}flex
YACC = ${BISON_ROOT}bison -y

LFLAGS = -L/opt/homebrew/Cellar/flex/2.6.4_2/lib -lfl
```

Find the `y.tab.c` label and replace:

```c
echo "extern  size_t  yyleng;";
```

With:

```c
echo "extern  int  yyleng;";
```

## 🚀 Run & Kill Xinu

Ensure you are in the `compile` directory:

```bash
make run   # To run Xinu
make kill  # Run in another terminal to stop Xinu
```

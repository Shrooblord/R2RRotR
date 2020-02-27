# Rayman 2: Revengeance: Rise of the Robots

*Rayman... the night sky is dark with the flags of the Enemy. Their horrid prison ships blot out the stars.*

*Our Energy is dimishing... we feel the end is coming. After they captured you, we lost all hope.*

*But I have sent you help. Together, I'm sure you can find a way out of your cage in the sky...*

*and save our lives.*



## Introduction
**Rayman 2: Revengeance: Rise of the Robots** (**R2RRotR**¹⁾) is a game built on top of the [RayMap](https://github.com/byvar/raymap) OpenSpace level viewing toolset and the [RaymapGame](https://github.com/Adsolution/RaymapGame) engine. Its aim is to deliver a brand new and hardcore **Rayman 2** experience, with all the areas and scenes you know and love from the game retouched and reimagined.

The **Pirate** threat is real, and your position weak. The chance for salvation is slim, but a true hero rises in the face of great adversity in order to vanquish their foe against all odds and save the day!

Featuring wholly custom enemy AI, pathfinding logic, newly designed game areas, challenging enemy encounter designs, dialogue, story, cutscenes, more complex combat mechanics, and more, this project was born out of a sincere passion for the original, and a growing itch to attempt to bring the game it inspired in my mind onto the digital canvas.

----

In a way, this project began its life back in 2011, when the first seeds of a **Rayman** fangame were born. Today, many of the best elements of that idea have split off and have joined the worldbuilding of my own franchise of games; though the lingering question "What if...?" always remained.

With my recent discovery of the **RayMap** tools, I became inspired to grab my childhood dreams and take them for a spin in the real world.

And so the development of my **Rayman 2** fangame, long since in the making, finally begun...

----
¹⁾ This is lovingly pronounced as [ɾɜʔtyːrɔtɜr] ("retúrrotr") like it's some sort of old-English Scandinavian artefact of a bygone era. What can I say. The man likes arcane-sounding symbols.

## Installation

### Playing the Game

For playing the game as an end-user, simply find the latest version in the [Releases](https://github.com/Shrooblord/R2RRotR/releases) page.


### Developing Your Own Stuff
So you want to build on top of what I've created, huh? Now that's the spirit!

The installation procedure is quite simple in concept, but there are some nuances different from most git repos that you need to be aware of.

#### Prerequisites
##### Windows
Unfortunately, as much of a fan I am of cross-platform solutions (using a variety of OSes myself), this project only supports the Windows operating system.

##### Unity
This project is coded in the [Unity](https://unity.com/) game engine, version *2019.3.0f6*.

##### Git Bash
We use the **Git Bash** shell for custom git commands in this project.

##### Symlinks on Windows
This project relies on sub-projects to function, all linked together into one "main" repository by using symlinks.

Unfortunately, creating symlinks of files the generic way on Windows causes git to submit the file as a hardlink instead, completely counteracting the point of using symlinks. So we'll have to install custom git commands in order to be able to work with our three sub-projects in tandem and maintain proper symlinks in git commits.

You need to modify your global user `.gitconfig` in your home directory for git (`C:\Users\YOURNAME\.gitconfig` in a typical installation) to add the following three commands:
`git add-symlink`; `git rm-symlinks` & `git checkout-symlinks`.

To do that, paste the following at the end of your `.gitconfig`file in order to create the three aliases required:

```

[alias]
	add-symlink = "!__git_add_symlink() {\n  if [ $# -ne 2 ] || [ \"$1\" = \"-h\" ]; then\n    printf '%b\\n'         'usage: git add-symlink <source_file_or_dir> <target_symlink>\\n'         'Create a symlink in a git repository on a Windows host.\\n'         'Note: source MUST be a path relative to the location of target'\n    [ \"$1\" = \"-h\" ] && return 0 || return 2\n  fi\n\n  source_file_or_dir=${1#./}\n  source_file_or_dir=${source_file_or_dir%/}\n\n  target_symlink=${2#./}\n  target_symlink=${target_symlink%/}\n  target_symlink=\"${GIT_PREFIX}${target_symlink}\"\n  target_symlink=${target_symlink%/.}\n  : \"${target_symlink:=.}\"\n\n  if [ -d \"$target_symlink\" ]; then\n    target_symlink=\"${target_symlink%/}/${source_file_or_dir##*/}\"\n  fi\n\n  case \"$target_symlink\" in\n    (*/*) target_dir=${target_symlink%/*} ;;\n    (*) target_dir=$GIT_PREFIX ;;\n  esac\n\n  target_dir=$(cd \"$target_dir\" && pwd)\n\n  if [ ! -e \"${target_dir}/${source_file_or_dir}\" ]; then\n    printf 'error: git-add-symlink: %s: No such file or directory\\n'         \"${target_dir}/${source_file_or_dir}\" >&2\n    printf '(Source MUST be a path relative to the location of target!)\\n' >&2\n    return 2\n  fi\n\n  git update-index --add --cacheinfo 120000       \"$(printf '%s' \"$source_file_or_dir\" | git hash-object -w --stdin)\"       \"${target_symlink}\"     && git checkout -- \"$target_symlink\"     && printf '%s -> %s\\n' \"${target_symlink#$GIT_PREFIX}\" \"$source_file_or_dir\"     || return $?\n}\n__git_add_symlink"
	rm-symlinks = "!__git_rm_symlinks() {\n  case \"$1\" in (-h)\n    printf 'usage: git rm-symlinks [symlink] [symlink] [...]\\n'\n    return 0\n  esac\n  ppid=$$\n  case $# in\n    (0) git ls-files -s | grep -E '^120000' | cut -f2 ;;\n    (*) printf '%s\\n' \"$@\" ;;\n  esac | while IFS= read -r symlink; do\n    case \"$symlink\" in\n      (*/*) symdir=${symlink%/*} ;;\n      (*) symdir=. ;;\n    esac\n\n    git checkout -- \"$symlink\"\n    src=\"${symdir}/$(cat \"$symlink\")\"\n\n    posix_to_dos_sed='s_^/\\([A-Za-z]\\)_\\1:_;s_/_\\\\\\\\_g'\n    doslnk=$(printf '%s\\n' \"$symlink\" | sed \"$posix_to_dos_sed\")\n    dossrc=$(printf '%s\\n' \"$src\" | sed \"$posix_to_dos_sed\")\n\n    if [ -f \"$src\" ]; then\n      rm -f \"$symlink\"\n      cmd //C mklink //H \"$doslnk\" \"$dossrc\"\n    elif [ -d \"$src\" ]; then\n      rm -f \"$symlink\"\n      cmd //C mklink //J \"$doslnk\" \"$dossrc\"\n    else\n      printf 'error: git-rm-symlink: Not a valid source\\n' >&2\n      printf '%s =/=> %s  (%s =/=> %s)...\\n'           \"$symlink\" \"$src\" \"$doslnk\" \"$dossrc\" >&2\n      false\n    fi || printf 'ESC[%d]: %d\\n' \"$ppid\" \"$?\"\n\n    git update-index --assume-unchanged \"$symlink\"\n  done | awk '\n    BEGIN { status_code = 0 }\n    /^ESC\\['\"$ppid\"'\\]: / { status_code = $2 ; next }\n    { print }\n    END { exit status_code }\n  '\n}\n__git_rm_symlinks"
	rm-symlink = !git rm-symlinks
	checkout-symlinks = "!__git_checkout_symlinks() {\n  case \"$1\" in (-h)\n    printf 'usage: git checkout-symlinks [symlink] [symlink] [...]\\n'\n    return 0\n  esac\n  case $# in\n    (0) git ls-files -s | grep -E '^120000' | cut -f2 ;;\n    (*) printf '%s\\n' \"$@\" ;;\n  esac | while IFS= read -r symlink; do\n    git update-index --no-assume-unchanged \"$symlink\"\n    rmdir \"$symlink\" >/dev/null 2>&1\n    git checkout -- \"$symlink\"\n    printf 'Restored git symlink: %s -> %s\\n' \"$symlink\" \"$(cat \"$symlink\")\"\n  done\n}\n__git_checkout_symlinks"
	co-symlinks = !git checkout-symlinks

```


Courtesy of [this](https://stackoverflow.com/a/16754068) **Stack Overflow** post.


#### Installation
Then just clone this repository as you would any other. It contains three sub-repos:
- **RayMap**, the main directory from which we run the **Unity** project and open our **Visual Studio** (other code editors may apply) IDE. As far as the engine and the IDE are concerned, this is the only folder of the project that "actually exists". This is because of the symlinks we will install in it as we develop our features. For the symlinks to the already existing submodules, no additional setup is required. It should just be plug-and-play. If you run into problems, please open an **Issue** on [the appropriate page](https://github.com/Shrooblord/R2RRotR/issues). **RayMap** is our main game engine.

- **RaymapGame**, the brains of the procedure. This codebase built on top of **RayMap** is a library with a host of convenience functions, scripting behaviour, and generic **RayMap** mod authoring tools.

- **Ray2ShrooTool** is the meat of the game. All the custom-built level authoring tools, enemy AI, level designs, scripting, story dialogue, cutscenes, etc. are contained here. It is built on top of **RaymapGame** and hooks into and relies on it heavily.

##### Unpacking from Git to Windows
Now that you have access to the git symlink commands from the **Prerequisites** installation step, we'll use the `rm-symlinks` one to change our git-friendly symlinks into Windows-friendly symlinks so that your development environment is all properly set up. Run this command in the main directory (`C:\...\R2RRotR`):
`git submodule foreach --recursive git rm-symlinks`

This will "unpack" all the symlinks for all submodules such that Windows can use them.

## Development
##### Respect the Sub-Project Structure
Additions made to [R2ShrooTool](https://github.com/Shrooblord/R2ShrooTool) should be made exclusively in the **R2ShrooTool** project directory, and pushed to the **R2ShrooTool** git repo (or your own fork of it). Only that way can I properly integrate your pull requests into the larger project!

Likewise, treat any of the other projects as their own distinct git repositories (in fact, they are!) and don't cross-contaminate your work across folders. If your project requires you to make three separate additions to the three submodules individually, then so be it!

Take care not to confuse symlinks within the **RayMap** area of the project for actual files.

##### Create your Visual Studio (or Other IDE) Environment
Use the `raymap` folder as the root of your project to mimic our in-house development environment.

##### Use the Git Symlink Commands We Installed

###### Reversing Unpack
In case you want to restore the git symlinks to their natural form (in a way git can use them but Windows cannot), run the following command:
`git submodule foreach --recursive git checkout-symlinks`

This returns your repo to its natural state, just as it was when you cloned it (as far as concerns the symlinks, that is).

###### Packing from Windows to Git
You can also commit your own symlinks in the main directory of the project (the **RayMap** submodule) to files and directories outside of it.

Let's look at an example of that. Say I have created a new script within **R2ShrooTool** that I want to include in the main game (**R2RRotR**). This means that, somewhere within the **R2ShrooTool** project directory, there now is a new file called `MyAwesomeAddition.cs`.

I.e., your script exists in a place like this: `C:\...\R2RRotR\R2ShrooTool\Assets\Scripts\AwesomeMod\MyAwesomeAddition.cs`

To get **R2RRotR** to recognise this file, we'll make a symlink to it. Create a new directory within the **RayMap** directory for your file:
`C:\...\R2RRotR\raymap\Assets\Scripts\AwesomeMod\`

Open your **Git Bash** shell, change directory to the **raymap** folder, and use the following command:

```
git add-symlink ../../../../../Ray2ShrooTool/Assets/Scripts/AwesomeMod/MyAwesomeAddition.cs Assets/Scripts/AwesomeMod/MyAwesomeAddition.cs
```

What does this do? Simply put, it creates a new symlink that tells the file system to look up 5 directories from where it is currently located (putting it in the `R2RRotR` root directory), and then down a series of folders to eventually find the source file `MyAwesomeAddition.cs`.

When you would now commit your changes to git, everyone, who pulls your branch into their own repo would receive a valid symlink inside the `raymap` directory to the file you created in the `Ray2ShrooTool` directory. When you launch **Unity**, the game will run, executing your new behaviour! When you open the `raymap` solution in **Visual Studio**, it will see your new script as if it existed within the `raymap` directory itself, and you can edit it in-place. The changes will be saved to the original file (so the one located in the `Ray2ShrooTool` directory).

Alternatively, if your `AwesomeMod` folder contains only assets you yourself created (perhaps you made this folder yourself?), you can symlink the entire folder rather than only the `MyAwesomeAddition.cs` file, and it will automatically pick up on any changes within the folder just like any normal folder would (i.e. all your scripts within that folder are automatically seen by **VS** and **Unity**). To do this:

```
git add-symlink ../../../../../Ray2ShrooTool/Assets/Scripts/AwesomeMod Assets/Scripts/AwesomeMod
```

##### Use Issues
Use the project's [Issues](https://github.com/Shrooblord/R2RRotR/issues) page to report bugs, submit pull requests, talk about new features, etc. etc..

## License
[QPL-1.0](https://opensource.org/licenses/QPL-1.0) for [R2ShrooTool](https://github.com/Shrooblord/R2ShrooTool) and the work done for the R2RRotR game; concerning the included [RayMap](https://github.com/Shrooblord/raymap) and [RaymapGame](https://github.com/Shrooblord/RaymapGame) sub-projects, all licensing rights reserved to the original creators of the projects. See their project homepages for more information.
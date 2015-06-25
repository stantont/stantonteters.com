+++
date = "2015-06-13T12:01:01-04:00"
draft = true
title = "Fixing Hugo's Bash tab completion on the Mac"
slug = "bash-completion-version-problem"
categories = [
    "Technology",
    "Development"
]
tags = ["bash", "command line", "hugo"]
+++
While experimenting with Hugo, the command line static site generator written in Go, I got an error when using Bash completion. I didn't save the error, but a search of my Google search history makes it look like it was something like this: 

{{< highlight bash >}}
hugo [tab] _init_completion: command not found
{{< /highlight >}}

I couldn't figure it out at first. Eventually I tried another application, git, and tab completion worked fine. Some searching led me to an issue on another application that mentioned the user had an old version of bash-completion.[^1] That didn't make an immediate impression on me. 

[^1]: https://github.com/ros/catkin/issues/485

I thought perhaps there was a problem with how the completion file was generated. 

I found that Hugo has a command to generate the completion file, so I tried using it. I did regenerate it, but that didn't help.

I went to the Hugo bash completion file and commented out the line `_init_completion -s || return`. That caused the error to stop, but bash completion also stopped working. 

I uninstalled and reinstalled bash-completion. I even tried uninstalling and reinstalling some of the applications that had bash completions.

I looked at the code in the Homebrew formula for bash-completion [^2] and saw the following comment:
{{< highlight bash >}}
# NOTE: version 2.0 is out, but it requires Bash 4, and OS X ships
# with 3.2.48. See homebrew-versions for a 2.0 formula.
{{< /highlight >}}

[^2]: https://github.com/Homebrew/homebrew/blob/master/Library/Formula/bash-completion.rb

At this point I recalled that article that mentioned the user had an old version of bash-completion and wished I'd paid better attention to it. So now it was just a matter of figuring out how to install (*tap* in Homebrew parlance) an alternate repo.  I had Bash 4 installed via Homebrew, so the inconsistency with the Mac's version of Bash didn't concern me.

`brew tap homebrew/versions` installed the repo containing bash-completion 2.

Then I uninstalled bash-completion and installed bash-completion 2:

{{< highlight bash >}}
brew rm bash-completion
brew install bash-completion2
{{< /highlight >}}

We're not done yet. After installing the application Homebrew displays a message saying it uses a new  directory to store the completions in, so you must change the bit of Bash config that loads the completions. The new code is:

{{< highlight bash >}}
if [ -f $(brew --prefix)/share/bash-completion/bash_completion ]; then
    . $(brew --prefix)/share/bash-completion/bash_completion
fi
{{< /highlight >}}

Now open a new terminal window and Hugo (and all your other completions) should work!

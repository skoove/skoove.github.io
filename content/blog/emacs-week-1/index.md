+++
date = 2025-08-17
title = "emacs week 1"
+++

I decided to try using emacs, because I had heard about it a lot and it seems like it can do a lot of things that my editor (helix) cannot. I have been very very pleasantly surprised about how it has been!

# packages
I use nix, so I would like nix to also manage my packages. The [nixos wiki page for emacs](<https://nixos.wiki/wiki/Emacs>) page details some ways to do this. One way is to use the community overlay and have it read your config, find `use-package` calls then install, but that would mean rebuilding my system for every emacs config change, which is a 1min + operation and I would like to be able to iterate and change little things a bit faster than that. That solution also felt more oriented to people who were already using emacs with an existing configuration that they just want to work and configure how they are used to configuration it. I chose to let nix do *all* of the package management.

``` nix
  programs.emacs = {
    enable = true;
    package = with pkgs; (
      (emacsPackagesFor emacs).emacsWithPackages (
        epkgs: with epkgs; [
		  # this is not all packages i have,
		  # just what i had when i was taking notes on this
          vertico
          orderless
          marginalia
          direnv
        ]
      )
    );
  };
```

Neat! now I only need to rebuild when I add a new package instead of for every config change. I just use home-manager to make an out of store symlink from my dotfiles to `~/.emacs` so that I do not need to rebuild for that either.

```nix
  home.file.".emacs".source = config.lib.file.mkOutOfStoreSymlink home/zie/.dotfiles/home-modules/emacs/emacs.el;
```

One last thing is to stop `use-package` trying to download anything, because that is nix's job, but I *do* want to use `use-package` to configure packages.

```emacs-lisp
(setq use-package-always-ensure nil)
```

Now that we have packages, I want to talk about everything I enjoy so far.

# enjoy
## meow (mrrp)
[meow](<https://github.com/meow-edit/meow>) is really really good. I come from helix, so it feels very familiar to me. I was going to build everything for actually editing from scratch but I am glad I gave up on that pretty quickly and just used `meow`.

## org
Holy shit. I have been trying to replace obsidian for so long, it is slow, not open source and just a bit yucky to use sometimes. I had heard of org before and is part of the reason I am trying emacs in the first place. I am still *not quite* sure how I want to use it, I am used to atomic notes for everything, but org seems like it may be more powerful with larger notes. I so far have used it to take notes for and draft 2 blog posts (this one and the [clippy one](/blog/just-wanting-to-help).

### font face things
Since I use emacs as the gui application, I can do some fun stuff with org font faces, like making headings bigger. I actually did this in the markdown mode too since I still write the posts in markdown. Something I would like to play with is a kind of live preview like obsidian has, as I really like that feature.

### typst math
I love typst, I am not a big fan of latex / mathjax. I can do typst maths using [org-typst-preview.el](<https://github.com/remimimimimi/org-typst-preview.el>). It is a bit more clunky to use than I am used to with obsidian (i need to manually turn on and off rendering and had to write my own function to toggle the whole buffer) but it is worth it for being able to write AND preview maths in a solid modal editor and with a sensible markup language like typst. 

Working out how to install it with nix took a little learning but I realised that it is not a package and in fact just a single elisp file so I just used `fetchFromGithub` and symlinked it into my `.emacs.d/lisp/` folder and then import it to the lisp config!

## multiple os windows (frames) for one editor
This one is great for me, I have a window manager I love (niri) so I want it to do the work of managing my windows. Since emacs can have mutliple os windows (called frames) I can let it do that. I just made a hotkey to make a new frame and I am set!

## custom git commit message editing mode
I like having some fancy things for my git commit message editing so that I can keep the header to 50 and wrap the body, I could not find anything to do this in emacs (except magit i think can do it, but i just use the cli for everything) so I made my own major mode for it. It warns me when I go over 50 characters in the first line and hard wraps at 76 characters.

```emacs-lisp
(define-derived-mode zie-git-commit-mode text-mode "ziegitcommit"
  "majour mode for git commit messages"
  (setq-local fill-column 72)
  (auto-fill-mode 1)
  (setq-local comment-start "#")

  (defun zie/git-header-warning (limit)
    "highlight header longer than 50 chars"
    (and (<= (line-number-at-pos) 1)
         (re-search-forward (format "^.\\{%d\\}\\(.*\\)$" (1+ 50)) limit t)))

  (font-lock-add-keywords
   nil `((zie/git-header-warning 0 font-lock-warning-face prepend)))
  (let ((map zie-git-commit-mode-map))
    (define-key map (kbd "C-c C-c")
		(lambda ()
		  (interactive)
		  (save-buffer)
		  (kill-buffer)))))

(add-to-list 'auto-mode-alist
	     '("/\\.git/COMMIT_EDITMSG\\'" . zie-git-commit-mode))
```

It is kind of shitty and barely works, In helix I just set 2 rulers but I could not find a nice way to do that in emacs :(. It is something I need to look at further.

# things i do not like
There are some things I do not like that much so far

## slow
~~Emacs is slow, this is the trade off for it's power, but it takes 700 ms to launch the client even when running in the client-server mode. Not much I can do about this :(.~~

edit 2025-08-18: This was a misunderstanding of what the dashboard was telling me, it was actually showing that the daemon took 700 ms to launch, launching new frames is significantly faster, though there is still a noticble slowness, for example when opening an org buffer.

## window and buffer management feels clunky
This is mostly because I am not used to it and because the default hotkeys suck a bit, I need to make something to just save and kill the open buffer, and find a nicer way to cleanup my buffer list without `C-x k` every time. 

Window management was helped greatly by not using windows and only using frames instead, it feels so good, I love it so much.

## need to restart `emacs.service` on many config changes
Sometimes I change something and need to restart the service, minor issue but probably something I can fix, I am thinking a function that evaluates a default config, then evaluates mine to essentially reset the config state.

## direnv switches enviroment every time i switch buffer
I use direnv to automatically use my nix dev environments, but when I switch between buffers it switches environment. I would prefer the environment be loaded locally for just that buffer but I do not know if that is possible. I will need to look into it.

# conclusion
I will probably keep using emacs for quite a while. It is a very good editor (when you download install and configure a good editor :p) but almost every default value feels very outdated, which is to be expected. I am loving how I can do seemingly everything I have wanted to do in helix but couldn't for whatever reason, and I am excited to keep using it.

If you use emacs and have advice, please let me know somewhere. Especially org-mode related, I am very excited to get more into that!

My config is available at: https://github.com/skoove/dotfiles/tree/main/home-modules/emacs

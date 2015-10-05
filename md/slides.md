<!-- .slide: data-background="images/docker.png" -->
<!-- .slide: data-background-size="cover" -->

# Haskell development with Docker

San Gillis

---

<!-- .slide: data-background="images/what-do-haskellers-want.png" -->
<!-- .slide: data-background-size="cover" -->

## What do Haskellers want? Over a thousand tell us

<aside class="notes">
    <ul>
    <li>Problems with cabal install -> cabal sandbox</li>
    <li>Problems with GHC install in general</li>
    </ul>
</aside>

---

<img src="images/docker_all_the_things.jpg">

---

<!-- .slide: data-background="images/sandbox.jpg" -->
<!-- .slide: data-background-size="cover" -->

## Docker > sandbox

* Not only sandbox Haskell packages, also GHC
* Create base image (e.g yesod)

---

<!-- .slide: data-background="images/sandbox.jpg" -->
<!-- .slide: data-background-size="cover" -->

<pre>
FROM haskell:7.10

RUN mkdir -p /root/.cabal
COPY cabal.config /root/.cabal/config
RUN cabal install aeson
</pre>

---

<!-- .slide: fullscreen-img="images/aeson.gif" -->

---

<!-- .slide: data-background="images/stack.png" -->
<!-- .slide: data-background-size="cover" -->

## What about stack?

Stack solves the same problems

---

<!-- .slide: data-background="images/stack_and_docker.png" -->
<!-- .slide: data-background-size="cover" -->

## Stack + Docker

Enable Docker in `~/.stack/stack.yaml`

<pre>
docker:
    enable: true
</pre>

Every `stack` invocation runs inside a Docker container

---

<!-- .slide: data-background="images/containers_stack.jpg" -->
<!-- .slide: data-background-size="cover" -->

## Stack + Docker

* Extension of stack image for custom environments
* Upgrade to new system tools/packages is easier
* CI integration
* Faster experimentation time

---

<!-- .slide: data-background="images/dev.png" -->
<!-- .slide: data-background-size="cover" -->

## Development

<ul>
<li>Syntax highlighting</li>
<li>Auto formatting</li>
<li>REPL integration</li>
<li>Auto-completion</li>
</ul>

---

<!-- .slide: data-background="images/dev.png" -->
<!-- .slide: data-background-size="cover" -->

## Spacemacs

Does a lot of these right out of the box

But can we integrate with a Docker setup?

---

## Auto formatting

`sgillis/stylish-haskell`

<pre>
(defun docker-stylish-haskell ()
  "Let a docker process run stylish-haskell on current buffer."
  (interactive)
  (with-current-buffer (current-buffer)
    (create-tmp-file)
    (shell-command
     "docker run --rm -v ~/.emacstmp:/file.hs sgillis/stylish-haskell > ~/.stylish-haskell-out")
    (erase-buffer)
    (insert-file-contents "~/.stylish-haskell-out")
    ))
</pre>

---

<!-- .slide: fullscreen-img="images/autoformatting.gif" -->

---

## REPL integration

<img src="images/demo_time.jpg">

---

## Extra dependencies?

`~/.stack/stack.yaml`

<pre>
docker:
    enable: true
    mount:
      - "/Users/sgillis/git/telegram"
      - "/Users/sgillis/git/hiphy"
</pre>

`giphybot/stack.yaml`

<pre>
resolver: lts-3.4
packages:
 - '.'
 - '../telegram'
 - '../hiphy'
 </pre>

---

## What's not there yet?

* Error messages
* Delete containers
* Unified handling of non-docker and docker ghci
* Autocompletion in REPL
* Autocompletion in files

---

<!-- .slide: data-background="images/production.jpg" -->
<!-- .slide: data-background-size="cover" -->

## Production

<pre>
FROM fpco/haskell-scratch

COPY .stack-work/dist/x86_64-linux/Cabal-1.22.4.0/build/giphybot/giphybot /giphybot
COPY libgmp.so.10 /usr/lib/x86_64-linux-gnu/libgmp.so.10
COPY libgcc_s.so.1 /lib/x86_64-linux-gnu/libgcc_s.so.1
CMD ["/giphybot"]
</pre>

A Docker image of just 29.24MB

---

## Q&A

https://github.com/sgillis

https://sgillis.github.io

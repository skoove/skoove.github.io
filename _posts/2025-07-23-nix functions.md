---
layout: blog-post
permalink: nix-functions
---
Functions in nix can be very confusing, as nix is very different to your traditional programming languages, being entirely functional.

## the basics
```nix
f = arg: arg + 1;
f 5 # evaluates to 6
```

Basic function syntax can be done as above, if we want multiple arguments, there are several ways to do it

```nix
add = num_1: num_2: num_1 + num_2;
add 1 3 # evaluates to 4
```

Add is a **curried**[^1] function and `num_1` is actually a function too!, it just wraps around the other one providing the ability for two arguments. Think of it like this, first we apply `num_1` leaving us with `num_2: 1 + num_2`, since we applied `num_1` we now have `num_2` and then by just applying that we get `1 + 3` which is 4. Curried functions are applied over several steps until we get a final value.

There is another, more boring way to do "multiple" arguments; attr sets.

```nix
add = { num_1 , num_2 }: num_1 + num_2;
add { num_1 = 1; num_2 = 6; } # evaluates to 7
```

Here, we have a attr set with keys `num_1` and `num_2`. To call the function we must construct a set with the keys inside it, this ends up being more verbose but is good when you want to be a bit more clear

## example

```nix
services.caddy = {
  enable = true;
  virtualHosts = let
    mkReverseProxy = {domain, ip}: {
      ${domain} = {
        extraConfig = ''
          tls internal
          reverse_proxy ${ip} {
            transport http {
              tls
              tls_insecure_skip_verify
            }
          }
        '';
      };
    };
    proxies = [
      { domain = "proxmox-1.home.com"; ip = "192.168.0.230:8006"; }
      { domain = "proxmox-2.home.com"; ip = "192.168.0.231:8006"; }
      { domain = "jellyfin.home.com"; ip = "localhost:8096"; }
    ];
  in
    builtins.foldl' (acc: val: acc // mkReverseProxy val) {} proxies;
};
```

Here, we have a few things, we define a function `mkReverseProxy` which takes in an attr set with the domain and ip to proxy to and makes the required configuration for caddy. Then we make a list of those function inputs in the proxy list, after that we use `builtins.foldl'` this function simply takes another function as it's argument. `builtins.foldl'` exists to recurse over a list applying a function to each input and accumulating the output. We call the function with the function `acc: val: acc // mkReverseProxy val`, which adds the result of `mkReverseProxy val` to `acc`, our augments to this function are `{}` and the list of proxies. `{}` is just the starting state of our accumulator, we don't need one so we use an empty set.

[^1]: <https://en.wikipedia.org/wiki/Currying>

+++
title = "nix functions"
date = 2025-07-23
+++

# the basics
```nix
let f = arg: arg + 1; in
f 5 # evaluates to 6
```

Basic function syntax is simple, we provide one argument and get one output, and a function can only have one input and output. But what if we wanted multiple arguments?

## multiple arguments

```nix
let add = num_1: num_2: num_1 + num_2; in
add 1 3 # evaluates to 4
```

Here `add` can take in 2 numbers `num_1` and `num_2` and return the result of adding both. But wait, didn't I just say that a function can only have one input and output? Yes, this function is two separate functions. The first function is `add` this function takes `num_1` and returns a function that takes `num_2` as an input. We can follow this by going through the function step by step.

1. `add 1 3`
2. `(num_1: num_2: num_1 + num_2) 1 3` [^anon_functions]
3. `(num_2: 1 + num_2) 3`
4. `1 + 3`

[^anon_functions]: You can write any function in parentheses like this, these are called anonymous functions, just meaning any function with no label. You only need to assign a function a name with the `let in` syntax if it needs it, otherwise you can just omit the name completely, for example if you were using the function as an input to another function.

Providing multiple arguments in this way in functional programming is called currying [^currying_wiki]

[^currying_wiki]: For more information see the Wikipedia article [wikipedia.org/wiki/Currying](<https://wikipedia.org/wiki/Currying>)

An alternative way of creating functions is attribute sets. In reality, we are actually only passing a single argument in — the attribute set.

```nix
let add = { num_1 , num_2 }: num_1 + num_2; in
add { num_1 = 1; num_2 = 6; } # evaluates to 7
```

An attribute set is just a set of keys and values. To say that we want our function to take in an attribute set, we provide a comma-separated list of the keys we should have enclosed `{}`, `{ num_1 , num_2 }:` followed by the function itself `num_1 + num_2`. To call the function we need to construct an attribute set, done by providing the keys and values separated by `;` and enclosed with `{}`. This is a more simple, but more verbose way of providing arguments, but is often the better choice for clarity when you have several arguments (in my opinion).

# example
```nix
{ ... }:
{
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
}
```

Here, we have a few things, we define a function `mkReverseProxy` which takes in an attribute with keys `domain` and `ip` which simply does some string interpolation to generate the [caddy](<https://caddyserver.com/>) configuration for reverse proxies. In the `proxies` array we create a few of the function inputs then use `builtins.foldl'`. Let's look closer at the `foldl'` call. `foldl'` takes in 3 arguments: a function to apply to each member of the list, `(acc: val: acc // mkReverseProxy val)` a starting state of the new value `{}` and the list itself `proxies`. The function we apply to the list simply appends the result of running `mkReverseProxy` on the value from `proxies` to the end of our accumulator (the attribute set with the starting state `{}`) using the `//` syntax for joining attribute sets.

---

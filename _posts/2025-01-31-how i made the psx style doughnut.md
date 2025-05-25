---
layout: blog-post
---
Photorealism is very hard, and the cycles renderer is very very intensive, especially on my GTX 960. So I decided to try something different and attempt a PSX style object.

<video autoplay loop style="width: 100%">  
  <source src="/assets/my work/3d art/doughnut.mp4" type="video/mp4">  
</video>

I want to clarify that when I say "ps1/psx style" I mean style, I am not going for a exact replica of the way those platforms looked, because that does not interest me, but I think that taking elements of that such as vertex wobble, and things that are not actually from those platforms, like colour compression and dithering and combining them with modern rendering techniques like reflection bloom and raytracing can have a very pretty result, and its something I want to experiment with much more in the future.

I first got the idea to pursue this style from [this video](<https://www.youtube.com/watch?v=2a_VtQJHkb8>) by [Retro Plus](<https://www.youtube.com/@RetroPlus>). This video shows their workflow with shoebox texture ripper and a [PSX vertex snapping addon by Lucas Roedel](https://lucasroedel.gumroad.com/l/psx_snapping). I took those two for this project, but the PSX vertex snapping addon had some issues that just would not work for my workflow so will not be used by me for most things in future.

The addon can get a very very authentic effect, but it comes at a cost. The addon works with a 3d grid which it snaps all the vertices too, and you can choose how large the grid is. I did not think about this very hard, and definitely did not consider that the amount of vertices would be $n^3$ when I selected 400. $400^3$ is 64 000 000, which took up 12 gigabytes of memory and was very slow.

I did not actually need a grid that large, 50 would have been plenty, but its still a lot more overhead than I was willing to have for an effect like that. I fumbled in geometry nodes for a while trying to round all the vertex positions to a set point but could not get that to work, and that would also not make the vertices to move when the object did not move, and that is realistic, but not what I want.

I gave up for a bit until I received this reddit comment:

> "For performance sake, did you consider a displacement modifier with a noise texture attached to a constantly-rotating object? It wouldn't be quite as accurate, but it would perform about 200% better."
> \- *[RatKingbutwithHumanChildren](https://www.reddit.com/user/Independent_Sea_6317/)*

So I now use that method, a noise texture on the object with an empty constantly moving.

For the textures, as mentioned earlier I used [shoebox texture ripper](https://renderhjs.net/shoebox/) and got this texture that I just painted a little to remove some harsh transitions.

![doughnut texture](/assets/my%20work/3d%20art/doughnut%20sprite.png){: style="image-rendering: pixelated" }

I also made a little shader for the doughnut to give the icing a little reflection

![the dougnuts shader](</assets/my work/3d art/doughnut%20shader.png>)

That is pretty much it! It was a lot of fun to make and I learnt a lot about the eevee renderer and how powerful it is so I think I will try and use it for future photorealistic-ish things!

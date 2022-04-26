# Particle-System


Index

1. Introduction

2. What is a Particle System and why it's important?

2.1 Origins

2.2 Particle systems used nowadays

2.3 The components

2.3.1 The emitters

2.3.2 The particles

3. The structure

3.1 Particle system module

3.2 Particle class

3.3 It’s all about memory!

3.4 Let’s talk about pools

4. More parameters

7. Performance

9. Useful References


## 1. Introduction
Particles are a key element in videogames, althought they may be unnoticed, they are those details that help the player get more deeply into the magic circle. They are also key on showing the quality of the product, with better or worse particle animations, and that's the most ironic thing, being just particles, apparently innsignificant elements, they manage to play a major role on the gameplay experience. Here I, Joan Damià, will explain you in detail what are particles and how to implement them in a 2D sdl game.

I will try to focus on showing you the heory around particles, some basic implementations in 2d and lots of references to the topic, so that you a hopefully more skilled coder than me, can explore and practice with particles. 

![giphy-downsized-large](https://user-images.githubusercontent.com/79161216/165123525-396444cb-e639-454e-8a20-74c2a78d0bc2.gif)


## 2. What is a Particle System and why it's important?

What is a particle system and why do we need one? 

First of all we have tot talk about sprites. Sprites, as you probably know, are two dimensional bitmaps displayed on screen that represent different elements like characters or items. 

![hero_spritesheet](https://user-images.githubusercontent.com/79161216/165140258-d315a4e2-6e50-4c23-8e23-6eb4d0c07e93.png)

When the level of detail rises, adding natural elements, the artist should draw all the details by himself for every sprite, and it would still feel weird because of the repeating animation. When we need something more organic like a fire or snow it has to be drawn by hand by the artist. If the game has a low-res pixel art style it may not be a problem, but as the level of detail and graphic power rises, making all of these details manualy can become a hard task.


And here is when particle systems appear.

A particle system is a structure that allows you to simulate particles of different types in an organic way with just modifying a base texture. The rest is made thanks to the program, that with the correct instructions, can take the texture and modify it, so that it creates the particles. 


### 2.1 Origins

Particle systems have become a key resource in many different productions that involve digital effects since the 80s, and it's understandable, as they are a quite simple way of simulating natural particle movement. 

William T. Reeves

Particles are, as you can imagine, individual elements in movement (randomized or not) that have a limited lifespan. Particles have to have a lifespan to simulate flow and a big source of elements. Particle systems are based on creating, destroying and guiding the movement of the particles needed. The fact that particles are (mostly) tiny individual elements that we can control (number and frequency of spawning and speed), we can simulate natural fenomenons like fire in a more simple way that just realisticly trying to program fire. 

![Fire_ashley](https://user-images.githubusercontent.com/79161216/165143383-9353838a-5dfa-4150-b8e5-808a06a495a0.gif)


### 2.2 Particle systems used nowadays

As said before, particle systems have turned out to be a must have system in movies, videogames and more, let's put some examples: Animated companies like Disney or Pixar use particle systems to represent natural or organic elements of the enviroment of their movies. 

https://youtu.be/7yorxr8KZIE

As you probably guessed, particle systems in videogames are also key in the developement to add detail to the different levels as well as making these details much easier. 

https://youtu.be/oTSzsTEwBEc

https://youtu.be/WbLlhsftLvI

As you have seen in the past examples, particles can be used to represent venomous smoke that fades away if you destroy the source, ambiance particles like dust or even water. PArticle systems make the job of creating these effects very easy thanks to the fact that the program does most of the job while the programmer just puts the parameters to get the effect they want.

At this point, what are the components of particle systems?

## 2.3 The Components

2.3.1 The emitters

Don't have to put much detail on what an emitter does right? Just in case, emitters are the main source of particles, with them we can control many important factors that will show different effects.

 -Emission rate, which stands for the amount of particles generated per frame.
 
 - The coordinates where the particles spawn

 -Particles lifespan.
 
 -Particles movement pattern and initial direction.
 
 -Physical properties like color, size...
 
The emitter also contains all the particles that will be updated and wiped out at the end of their lifespan. This information is crucial, as it's transfered to the particles so that they behave as desired. 

It is important to say that emitters can be created in other emitters to put more detail into the particles of a certain area or element, for example, the flamethrower from Battlefield:

https://youtu.be/3NzBabyctCI

2.3.2 The particles

The particles themselves are textures moving on screen, just that. The trick is that modifying them with the data from the emitters, we can make them look like many things: Snow, water, fire, dust, sand, embers or even cinder. It all depends on the rate of spawning, their movement, if they are big or small...

Many times, the textures that are used to represent the particles look like this

![f300aa6fe4b2047972217b6226d0dca7](https://user-images.githubusercontent.com/79161216/165149500-e83df52c-9470-48c7-8288-94b766d91f5a.jpg)

They doesn't look useful in this scale and their appearence. However, with the right indications we can transform them so that they create the ilusion we want. 

In our case, our particle system is based in a 2D scenario. There are many ways to approach the creation of a particle system, this is one of them


## 3. The structure
Here there is an example on how the system is made. I have to say that at this point, this isn't my work, this part is extracted from another github project about particles that excelently explains how to make one. 

So basically we have a module called j1ParticleSystem that will contain a list of emitters. Inside these emitters we will have all the data we have gotten from the xml and they will contain a pool of particles that will be also updated and rendered on screen.

In case you wonder how the whole code is organized here’s a simple scheme. Our application has a module for each category call. Our application code is structured in modules. The main module (called j1App.cpp) manages all the other modules calling in a loop its respective awake, preupdate, update, postupdte, cleanup that they share thorugh a base class j1Module. So our j1ParticleSystem will be one of these modules that will update all the emitters that at the same time will update its respective particles. Down below a basic scheme of this shows what has been explained:


4.1 Particle system module
This is the module in charge of everything that happens with our particles. We will not cover how it works as it’s something generic that can be implemented according to your needs and this tutorial is focused on the particles. With this module you can:

Destroy an existing emitter
Destroy all the emitters
Add an emitter
Update all the emitters
Control emitters destruction
Read all the data from the xml file to pass it to the emitter each time we create one. As you’ve seen on the UML we have a vector containing all the emittter data for each type of emitter defined on the xml file. This is the structure of a emitter defined in the xml file:
      <emitter type ="fire">
        <angleRange min ="80" max ="110"/>
        <rotSpeed value ="0" randMin = "-1" randMax ="1"/>
        <startSpeed value ="200" randMin = "0" randMax ="1"/>
        <endSpeed value ="200" randMin = "0" randMax ="1"/>
        <startSize value ="200" randMin ="0" randMax ="1"/>
        <endSize value =" 80" randMin ="1" randMax ="1"/>
        <emitNumber value ="1"/>
        <emitVariance value ="0" randMin = "0" randMax ="1"/>
        <maxParticleLife value ="100" randMin = "0" randMax ="1"/>
        <textureRect x="0" y="0" w="128" h="128"/>
        <startColor r ="255" g="100" b="0" a="255"/>
        <endColor r ="210" g="210" b="210" a ="0"/>
        <blendMode mode ="add"/>
        <lifetime value ="-1"/>
      </emitter>
4.2 Particle class
The particle class is very simple, just a lot of data that will define its behavior and a couple of methods to draw and updated its position. Let’s see how we approach this. We will consider a particle as a moving point in space. It will have a postion, a velocity and a texture at least. Here’s a simple diagram of a basic particle:

particle_scheme

So basically a particle will be a class with position, a vector velocity and texture (represented by a rectangle indicating the position and size inside the atlas). In this case we will add a lot more data but that’s something optional depending of what you want to do. This particle will have a constructor for setting everything up, an update method to move them and a draw one to render them on screen and that’s it.

class Particle
{
private:
	uint startLife;
	float posX, posY;
	float velX, velY;
	SDL_Rect pRect;
	
public:
	Particle();
	void Update(dt);
	void Draw();
} ;
Inside the Update() method we will move our particle according to its velocity. To calculate its velocity we just need the angle that will come from the emitter and the speed norm. It will be something like this:

velX = speed * cos(angle);
velY = speed * sin(angle);

posX += velX * dt;
posY += velY * dt;

life--;
And that’s pretty much it. Update and move in loop. Of course the system will have a lot of more properties but for now let’s keep it simple.

This is very nice but have a problem, a huge one. But the problem here resides in the fact of how generate a constant flow of particles in our code. Well, let’s see how.

4.3 It’s all about memory!
The pool is the most important and fundamental part of the system, everything else is secondary; you can always add more data to the particles whenever you need it.

Let’s adress the big elephant in the room. If we need to generate a constant of flow of particles that are born and die over time that means we have to constantly create an destroy particle objects using the heap. And that’s not a good thing for our CPU. Let’s see why.

When you write the ‘new’ operator in your program you allocate enough memory in the heap so the new object can fit in there. But what happens if we are constantly creating and destroying particles hundreds of time per frame? Well, nothing good for sure. Let’s highlight the main problems.

Memory fragmentation: as you know memory is a continuous space divide in bytes. If we constantly allocate and destroy memory we will have what’s called ‘memory fragmenation’. This means that all the data is not allocated next to each other but there’s some gap in between. This happens because we’ve destroyed a particle that was allocated in the middle of other particles and now we have small gaps that occupies space but cannot be filled with other particles as they might not be small enough. We have free memory but cannot use it. As Bob Nystrom says in his article about Pools:
“It’s like trying to parallel park on a busy street where the already parked cars are spread out a bit too far. If they’d bunch up, there would be room, but the free space is fragmented into bits of open curb between half a dozen cars.”

mem_frag

Framerate drop: another usual thing that can happen in this scenario is bad performance in our application. This has to do with the garbage collection. The garbage collection is the process which the computer uses to manage resources automatically. It is in charge of freeing up memory when it’s no longer needed. This process is usually done under the hood and we don’t actually notice anything. For example, when we declare a variable inside a function and then this function has finsihed its execution this variable will be destroyed from memory as it lives only within the function scope. However, when freeing up mempory at high rates can cause the programe to use a lot of resources to take care of that resulting in a lag or frame drop in our application.

Risk of memory leaks: if you you’re constantly allocating memory it’s very easy to have memory leaks as you might not dlete everyhing you need to. Hence, your memory managment is poorly done and your wasting more than you need.

It’s all about memory! It’s a not infinite but valuable resource and we must take care of use it wisely. To avoid this let’s introduce the pool.

4.4 Let’s talk about pools
A pool is simply container of reusable objects. This means that when objects are extracted from the pool they can go back to it when they are no longer needed by the program. It’s like a fountain of water that have a closed loop of reusable water. That’s perfect for our particles. We no longer need to create and destroy particles, we can allocate enough space for the pool and use always the same particles; that’s great, isn’t it?

To implement this pool we will follow the great implementation done by Bob Nystrom in his article of his book Game Programming Patterns.

Let’s start by defining the ParticlePool class:

class ParticlePool
{
private:
	
	int poolSize = 0;
	Particle* firstAvailable;
	Particle* particleArray = nullptr;

public:

	ParticlePool(Emitter* emitter);
	virtual ~ParticlePool();

	//Generates a new particle each time it's called
	void Generate(float posX, float posY, velX, velY, pRect);
	
	// Update (move and draw) particles in the pool. If there are no particles alive returns false
	bool Update(float dt);
};
Our pool will store particles inside an static array. Too keep track of all the particles each particle will have a pointer to the next one. Let’s see the methods to take a look at this.

In the constructor we will allocate enough memory for our pool. We will talk about how to calculate the pool size later (this will be done in the emitter class). This will be done just one time, when a emitter is created. When we have reserved the memory we need to link all the elements like in a forward linked list,; this is called a free list. For doing this each particle will have a pointer to the next one. Moreover, we will have a special pointer called ‘firstAvailable’ that will always point to the particle that will be released. For now it will pointing to the first element.

// This pool constructor sets our particles to available
ParticlePool::ParticlePool(Emitter* emitter)
{
	// Fill the pool according to poolSize needed for the emitter
	poolSize = emitter->GetPoolSize();
	particleArray = new Particle[poolSize];

	// The first particle is available
	firstAvailable = &particleArray[0];

	// Each particle points to the next one
	for (int i = 0; i < poolSize - 1; i++)
		particleArray[i].SetNext(&particleArray[i + 1]);

	// The last particle points to nullptr indicating the end of the vector
	particleArray[poolSize - 1].SetNext(nullptr);
}
Okay, let’s see now how the Generate method. In this method we will generate a new particle. How? Well, first we’ll check the pool is not empty. Then we store the first available particle in an auxiliar pointer and the firdtAvailable pointer will point shift and point to the next one in the array. Finally we call an init method to initialize all the particle attributes. This generate method will be called inside the emitter update method as many times as we want create particles per frame.

	void ParticlePool::Generate(posX, posY, speed, pRect)
{
	// Check if the pool is not full
	assert(firstAvailable != nullptr);

	// Remove it from the available list
	Particle* newParticle = firstAvailable;
	firstAvailable = newParticle->GetNext();

	// Initialize new alive particle
	newParticle->Init(posX, posY, speed, pRect);
}
In the update method we will loop through the entire pool and check if the particles inside it are alive. If it’s the case we update it and then draw it. If not the particle will become the first available in the pool. We can update particles and then render them or the other way around. Both ways have advantages and downsides.

Update then Render: particles won’t spawn exactly at the point where the emitter is because in the first frame they will move first and then render.
Render then Update: particles will spawn where they are supposed to. However, particles won’t react to user motion input until next frame.
We will choose the first option as spawn point is a problem that we can do some workarounds as input delay is not.

bool ParticlePool::Update(float dt)
{
	bool ret = false;

	for (int i = 0; i < poolSize; i++)
	{
		if (particleArray[i].IsAlive())
		{
			particleArray[i].Update(dt);
			particleArray[i].Draw();
			ret = true;
		}
		else // if a particle dies it becomes the first available in the pool
		{
			// Add this particle to the front of the vector
			particleArray[i].SetNext(firstAvailable);
			firstAvailable = &particleArray[i];
		}
	}

	return ret;
}
You’ve probably seen that particles now are a little bit different. Now for particles we will use unions. Unions are data structures that only uses the memory of the variable that is being used. For our particle we will have a union with a struct inside that will hold all the data of a living particle. Outside the struct a pointer that will be only used when the particle is available in the pool. The only variable that will be outside the union is the particle life variable that will only be relevant to know if the particle is alive or dead.

The next implementation is based on the implementation of a particle system done by Bob Nystrom in his article of his book Game Programming Patterns.

	/*  This is the only variable we care about no matter if
	   the particle is alive or dead */
	uint life = 0;
	
	union ParticleInfo
	{
		/* This struct holds the state of the particle when 
		   it's being update (it's still alive).*/
		struct ParticleState
		{
			uint startLife;
			float posX, posY;
			float velX, velY;
			SDL_Rect pRect;
			ParticleState() {}

		} pLive;

		/* If the particle is dead, then the 'next' member comes 
		   into play and the struct it's not used. This pointer
		   called 'next' holds a pointer to the next available 
		   particle after this one. */
		Particle* next;

		ParticleInfo() {}
	} pState;
Okay that’s pretty much it. The only thing that we need to know is how to know the size of the pool. If we supose all the particles have the same life and know how many particles are generated per frame (emission rate) then can do the math. Let’s do an example. Imagine we generate 3 particles per frame and our particles have a maximum life of 2 frames.

poolsizeproblem

The obvious thing would be to do poolSize = particleMaxLife * emissionRate. It’s close but not enough. If we generate particles particles from the pool before updating then then will have a frame where the pool will be empty. So we need to do:

poolSize = (particleMaxLife + 1) * emissionRate;
And that’s it. The emitter class will not be covered but it’s quite simple. The important thing is the pool.

5. More parameters
But as you’ve seen we had a lot of parameters in xml file to tweak the particle behavior. In this table there’s all the particle properties explained:

ATTRIBUTE	DESCRIPTION
Angle range	Angle spectrum of the particle flow.
Rotation speed	How fast you want the texture to spin.
Speed	How fast particle move.
Start and end size	Particle size when born and when die.
Emit number	How many particles are generated per frame.
Emit variance	An offset to randomiza particle generation.
Particle life	How many frames a particle lives.
Texture rect	The rectangle of the atlas renderer from the atlas.
Particle colors	Start and end color of the particle. We use linear interpolation for this.
Blend mode	How colors interact wich each other. We can create glow of effects using additive blending in SDL.
Life time	How many seconds an emitter lives.
We can have a lot of randomization to make the effect even more organic. That depends on you.

So if we want to create a fire what we need to do is to to change the start and end color, put the blending mode to additive to make it glow, reduce alpha color over time… And will get something like this.

torch_gi

But we can do explosions too:

explosions

Another interesting thing is too play with the particle movement. We have done a linear movment but we can simulate paraboles, accelerated movement, circular and so on. A really cool and simple thing to do is to implement turbulunce in form of vortices. To put it simple, a vortex is like an spinning air wheel that causes objects to change it’s movment in a circular and chatoic way. We can simulate this by using a simple ad-hoc formula. In this article is explained how it’s done.

The vortex is very simple:

	struct Vortex
	{
		float posX, float posy;
		float speed;
		float scale;
	}
And then we need to modify particle movement like this:

	float dx = pState.pLive.pos.x - vortex.pos.x;
	float dy = pState.pLive.pos.y - vortex.pos.y;
	float vx = -dy * vortex.speed;
	float vy = dx * vortex.speed;
	float factor = 1.0f / (1.0f + (dx * dx + dy * dy) / vortex.scale);

	pState.pLive.pos.x += (vx - pState.pLive.vel.x) * factor + pState.pLive.vel.x * dt;
	pState.pLive.pos.y += (vy - pState.pLive.vel.y) * factor + pState.pLive.vel.y * dt;
And we can get really cool things like this:

particles

If we set vortex speed to 0 particles will slow down when they get coloser. We can even do implosions!

implosions


7. Performance
Performance of particle systems really depends of how many particles are being rendered and moved at the same time on screen. At the end of the day, any particle system will eventually crash above a certain number. The higher the number the better the performance.

In our case it handles particles really well but framrate starts to drop when we have have about 2000 particles being rendered and moved on screen. If we have a vortex performance is worse as it needs to do a lot calculations for each individual particle.

Here are some examples of performance using Brofiler:

If the number is not to high we hava an stable framerate of 60 fps and the pool just takes about 0,2 ms to update and render all the particles which is less than a 2% of the frame:
brofiler2

If the number is high enough like 2000 then framerate can drop to 30 fps or less. And the pool takes about 18 ms to update and render the particles which is more than the 50% of the frame:
brofiler

However, overall the system has a good performance if it’s not overused.

Back to index

8. Useful References
Webs and forums:

http://buildnewgames.com/particle-systems/
http://rbwhitaker.wikidot.com/2d-particle-engine-2
http://lazyfoo.net/tutorials/SDL/38_particle_engines/index.php
https://stackoverflow.com/questions/12657962/how-do-i-generate-a-random-number-between-two-variables-that-i-have-stored
http://gameprogrammingpatterns.com/object-pool.html
https://www.alanzucconi.com/2016/01/06/colour-interpolation/
http://rbwhitaker.wikidot.com/2d-particle-engine-4
http://rbwhitaker.wikidot.com/additive-sprites
http://particle2dx.com/
https://wiki.libsdl.org/SDL_SetTextureColorMod
http://natureofcode.com/book/chapter-4-particle-systems/
http://www.karlsims.com/papers/ParticlesSiggraph90.pdf
https://www.lri.fr/~mbl/ENS/IG2/devoir2/files/docs/fuzzyParticles.pdf
https://www.lri.fr/~mbl/ENS/IG2/devoir2/files/docs/particles.pdf
https://www.gamasutra.com/view/feature/131565/building_an_advanced_particle_.php?page=2
http://archive.gamedev.net/archive/reference/articles/article1982.html
https://gamedevelopment.tutsplus.com/tutorials/adding-turbulence-to-a-particle-system--gamedev-13332
https://es.khanacademy.org/computing/computer-programming/programming-natural-simulations/programming-particle-systems/a/intro-to-particle-systems
https://docs.unity3d.com/Manual/PartSysWhatIs.html
http://archive.gamedev.net/archive/reference/articles/article2002.html
https://www.khanacademy.org/partner-content/pixar/effects/particle/v/effects-overview
https://www.khanacademy.org/partner-content/pixar/effects/particle-physics/a/start-here-fx
http://www.di.ubi.pt/~agomes/tjv/teoricas/09-particles.pdf
https://gamedevelopment.tutsplus.com/tutorials/object-pools-help-you-reduce-lag-in-resource-intensive-games--gamedev-651
https://en.wikipedia.org/wiki/Perlin_noise
Videos:

https://www.youtube.com/watch?v=vdgiqMkFygc
https://www.youtube.com/watch?v=FEA1wTMJAR0
https://www.youtube.com/watch?v=rR_bm8f8rVE
https://www.youtube.com/watch?v=heW3vn1hP2E
https://www.youtube.com/watch?v=OXK2Xbd7D9w&index=1&list=PLZlv_N0_O1gYDLyB3LVfjYIcbBe8NqR8t
https://www.youtube.com/watch?v=QXbWCrzWJo4
https://www.youtube.com/watch?v=ovlVh-QgVao
https://www.youtube.com/watch?v=FUu2WNcJbtE
https://www.youtube.com/watch?v=G6OGKP3MaUI
Sprites and images:

https://opengameart.org/content/lens-flares-and-particles
https://dribbble.com/shots/1925630-Explosion

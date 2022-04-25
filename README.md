# Particle-System


Index
1. Introduction
2. What is a Particle System and why it's important?
2.1 Looking the past
2.2 Particle systems used nowadays
2.3 The components
2.3.1 The emitters
2.3.2 The particles
3. Our approach
4. The structure
4.1 Particle system module
4.2 Particle class
4.3 It’s all about memory!
4.4 Let’s talk about pools
5. More parameters
6. TODOs
6.1 TODO 1 - Getting the emitter data
6.2 TODO 2 - Let’s introduce the pool
6.3 TODO 3 - Improving the pool
6.4 TODO 4 - Color Interpolation & blending
6.5 TODO 5 - Fire and Smoke
6.6 BONUS CODE - Vortices
7. Performance
8. Improvements and further work
9. References
10. License

## 1. Introduction
Particles are a key element in videogames, althought they may be unnoticed, they are those details that help the player get more deeply into the magic circle. They are also key on showing the quality of the product, with better or worse particle animations, and that's the most ironic thing, being just particles, apparently innsignificant elements, they manage to play a major role on the gameplay experience. Here I, Joan Damià, will explain you in detail what are particles and how to implement them in a 2D sdl game.

I will try to focus on showing you the heory around particles, some basic implementations in 2d and lots of references to the topic, so that you a hopefully more skilled coder than me, can explore and practice with particles. 

![giphy-downsized-large](https://user-images.githubusercontent.com/79161216/165123525-396444cb-e639-454e-8a20-74c2a78d0bc2.gif)


## 2. What is a Particle System and why do we care?
What is a particle system and why do we need one? Well, first let’s dig a bit into computer graphics stuff.

If you have programmed games or computer graphics related stuff is quite common to work with sprites and sprites sheets. For those unfamiliar with this term, a sprite is simply a two-dimensional bitmap used to be displayed on the screen. It is used especially in 2D videogames to render both static and animated elements of the game. It’s also very frequent to find them as a pixel art form. To save memory they are normally grouped into a same bitmap called sprite sheet. Down below there’s an example of a sprite sheet of Fire Man from the NES Mega Man game:

77885

This example is very simple but sprites can become complex…

super_sonic_1_sprite_sheet_by_winstontheechidna-dbhwb26

…a lot actually…

sm

So, what happens when we need something more organic, more chaotic; like a fire, a smoke, a fog, fireworks, snow… Yes, you’ve guessed it. It has to be drawn by hand by the artist. If the game has a low-res pixel art style it may not be a problem, but as the game becomes more detailed natural elements are more difficult to reproduce and if you have to timeline for your project this is a problem.

This is a spritesheet of a fire caused by a a bomb in Gunbird (Psiskyo, 1994):

tetsu_explosion_death

You start to see the problem now right? What if the game is too complex to do it by a hand or is actually a 3D game? What if we want something like this:

explosion_01_dribbble

Well, that’s when particle systems come into play.

A particle system is a structure that allows you to simulate particles of different types in an organic way with the advantage of just tweaking some particle properties to get the exact results you want very quickly and without effort. And the best part: you don’t have to draw any of these particles. You just need a base texture and that’s all. The system will be in charge of rendering the particles, the effects (such as glowing), physics calculations, emission rate and so on. You don’t need to worry about anything, just playing around with it to get what you want.

This is just a generic description. Let’s dive into more techical details of what this all means.

Back to index

2.1 A bit of history
Particle systems are very common in computer graphics and have been used since the early 1980s. One of the first implementations was seen in Star Trek II: The Wrath of Khan in 1982. The film featured a sequence called Genesis Demo where a planet is terraformed by a torpedo to make it habitable. For achieving this effect William T. Reeves, a researcher at Lucasfilm Ltd, did a research on particle systems on a paper called Particle Systems - A Technique for Modeling a Class of Fuzzy Objects.

This is the sequence mentioned above, as you can see the results are quite impressive considering the time this was done:


Reeves describes a particle system in this paper as follows:

“A particle system is a collection of many minute particles that together represent a fuzzy object. Over a period of time, particles are generated into a system, move and change from within the system, and die from the system.”

So basically a particle system contains a bunch of tiny objects called particles that have some kind of movement and a lifetime. These particles are generated and destroyed over time to simulate a flow. Hence, a particle system is the structure in charge of managing the living particles and generate new ones when needed.

Organic effects are difficult to simulate but what particle systems do is simplify the problem. Like in physics, we divide the effect in tiny and controllable particles that as a whole seems like something bigger that behaves as natural phenomenas. By managing a large group of these particles we can represent natural effects like water, fire or smoke so much easier than if we tried to treat it as a whole thing.

2.2 Particle systems nowadays
Since the Genesis effects things have evolved quickly but the foundation is the same as before. Here’s a short video on how Pixar Animation Studios uses particle systems nowadays. William T. Reeves is currently working there too!


Nowadays particle systems can be found in any almost any game engine or 3D software. Maya and 3D Max have one as well as Unity or Unreal Engine. Particle are really important in a lot of videogames as it makes huge improvement. Particles makes the enviroment or the the player stands out and it really adds up a lot to the game. Let’s see a few examples.

Take for example the next teamfight in League of Legends, as you can see particles makes a huge diiference when producing things like magic spells:

LOLGIF

Now try to picture it without all this particle effects, it wouldn’t’ make sense right?

But particle systems are also used to generate more realistic things like these explosions in Battlefield:

battlefield

Or more subtle like the main menu in Hollow Knight:

animated gif-downsized_large

Moreover, particle systems can be a double-edged sword too. Aside from being used for creating VFX as well as hiding visual bugs. For instance in fighting games collision geometry is usually hidden by particles to hide erros like 3D models intersection.

stnerf

Okay now that you get the idea let’s see what composes a particle system.

2.3 The components
2.3.1 The emitters
Particle systems usually contain what it’s known as an emitter. An emittter is the object in charge of spawning all the particles and defining the particles behaviour and their properties. An emitter is in charge of the following things:

Controlling the emission rate of particles. Basically how many particles are generated per frame.
The postion where the particles are generated.
Particles’ velocity and movement.
Particles life.
Other particle properties like colors, transparency or size.
The emitter itself contains all the particles that will be updated and die over time. All this data is transfered from the emitter to all this particles so they behave like they are suposed to. Changing this data will change how the particles in that emitter will behave.

A particle system can be composed of multiple different kind of emitters that at the same time can also have emitters inside of them. This inception thing can go as far as we want depending of what we want to simulate. For example, for the Genesis Effect Reeves explains in his paper that they used a tree structure in order to create this subemitters. By using this hierearchy there’s a particle system which acts as a parent and contains subsystems that contains other subsystems.

2.3.2 The particles
Now that we have the base, let’s talk about particles. As said before particles inherit its properties from the emitter they came from. But what is exctly the particle itself? The particle itself it’s just a simple moving texture that is renderered on screen. In 3D enviroments they use something called billboards which is basically an image that always faces the camera. But we don’t need to worry about that as we will focus only in 2D.

This texture can be anything we want that fits our purpose. It’s usually a balck and white fuzzy texture that will be tinted when rendered. A good example is this set of particle textures down below:

particle_set

Particle properties along with the emitter can be anything you like. The possibilities are endless. The Unity engine for example has big spectrum when it comes to emitters and particle properties, you can change anything you want and this flexibility allows you to generate almost any type of particle you can think of. You can choose the size, the color, the speed, the emitter shape, the texture and a lot more. Down below there’s a screenshoot of this options in Unity.

psystem_unity

But how this particles are managed thorugh the system? do we need to generate a completly new particle each time? Will this affect the perdormance? We will discuss this in the next section.

When working in 3D enviroments particle textures are often mapped in a 3D geometry to create more interesting effects. This is called blending geometry and it’s quite impressive! Here’s a quick video on how particles are done in League of Legends using this technique.


Okay, I think that’s enough theory for now, we must build the system in order to use it. So let’s get to work!

Back to index

3. Our approach
Okay but what are we going to do? We will keep it simple but solid and flexible. Our particle system will have the following features:

It will be 2D (although almost everything explained here can be transfered into 3D if needed).
Particles movement will be linear but with the option of interpolate between start and end speed.
An atlas full of particle textures will be used to render different types of particles.
All data wil be outside the code, written in an xml file.
A pool will be used as a container for emitter particles. We will talk about what this means soon, for now everytime we talk about pools just think of a data container like an array of particles.
Diferent type of emitters, hence diferent particles will be created through parametrization. This means will only have an emitter and particle class and depending of the data they both recieve the effect will be different.
Emitter properties will be covered more in depth later but they will be the next ones:
Angle range of particles’ flow
Rotation speed of particles
Particles’ speed
Particles’ size
Emission rate
Particles’ life
Particles’ texture
Particles’ colors
Particle’s blend mode
Emitter life
To create an emitter we will call a function in our scene that will return a pointer to the emitter. With this pointer we can call emitters methods for stoping or starting its emission or simply to destroy it. At the end we want to have something like this:
emitter = AddEmitter(positon, EMITTER_TYPE);
4. The structure
Down below there’s a simple scheme of the structure of the system:

uml

So basically we have a module called j1ParticleSystem that will contain a list of emitters. Inside these emitters we will have all the data we have gotten from the xml and they will contain a pool of particles that will be also updated and rendered on screen.

In case you wonder how the whole code is organized here’s a simple scheme. Our application has a module for each category call. Our application code is structured in modules. The main module (called j1App.cpp) manages all the other modules calling in a loop its respective awake, preupdate, update, postupdte, cleanup that they share thorugh a base class j1Module. So our j1ParticleSystem will be one of these modules that will update all the emitters that at the same time will update its respective particles. Down below a basic scheme of this shows what has been explained:

code_scheme

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

Back to index

6. TODOs
6.1 TODO 1 - Getting the emitter data
Load emitter data into the emitter data vector:

We just want to load the emitter data from the “fire” emitter for now
The for loop already parses the xml file for you. Just search for the emitter type “fire”.
Once you find it use LoadEmitterData() to fill the vector. Use EMITTER_TYPE_FIRE for the enum.
SOLUTION

std::string emitterType = emitters.attribute("type").as_string();

		if (emitterType == "fire")
			LoadEmitterData(emitters, EmitterType::EMITTER_TYPE_FIRE);
6.2 TODO 2 - Let’s introduce the pool
TODO 2.1:

Declare an static array of 100 particles inside the ParticlePool header.
SOLUTION

Particle particleArray[100];
TODO 2.2 - Convert particleArray into a free list:

Make the firstAvailable pointer point to the first element of the pool
Make each particle inside the pool point to the next one. Use the SetNext() method
Make the last particle point to nullptr
SOLUTION

	// The first particle is available
	firstAvailable = &particleArray[0];

	// Each particle points to the next one
	for (int i = 0; i < 100; i++)
		particleArray[i].SetNext(&particleArray[i + 1]);

	// The last particle points to nullptr indicating the end of the vector
	particleArray[poolSize - 1].SetNext(nullptr);
TODO 2.3 - Generate a new particle from the pool:

Use firstAvailable to Init the particle.
But remember to move the firstAvailable pointer to the next particles so we don’t lose track of it.
Also check first that the pool is not empty before doing anything. Use assert for this.
SOLUTION

// Check if the pool is not full
	assert(firstAvailable != nullptr);

	// Remove it from the available list
	Particle* newParticle = firstAvailable;
	firstAvailable = newParticle->GetNext();

	// Initialize new alive particle
	newParticle->Init(pos, startSpeed, endSpeed, angle, rotSpeed, startSize, endSize, life, textureRect, startColor, endColor, blendMode);
TODO 2.4 - Update and draw living particles in the pool.

If it’s alive update it, draw it and make sure to return true
If a particle is dead it becomes the first available in the pool. Use IsAlive() method to check this.
SOLUTION

	bool ret = false;
	
	for (int i = 0; i < 100; i++)
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
When you’re don you should get something like this:

todo1

But what happens if particles have 100 of life? We don’t have enough particles in the pool. Let’s fix this.

6.3 TODO 3 - Improving the pool
TODO 3.1 - Calculate pool size:

Come up with a simple formula to know how big the pool must be. Only one line of code!
The number calculated must be always big enough so the pool it’s NEVER EMPTY.
Hint: the variables maxParticlesPerFrame and maxParticleLife play a big role here.
Pencil and paper will help!
SOLUTION

	// Pool size calculations
	poolSize = maxParticlesPerFrame * (maxParticleLife + 1);
TODO 3.2 - Allocate memory for the pool:

Use GetPoolSize() from the given emitter.
Remember to free the memory!
Once this is done try to execute again. It should not have any pool size problems anymore!
SOLUTION

	// Fill the pool according to poolSize needed for the emitter
	poolSize = emitter->GetPoolSize();
	particleArray = new Particle[poolSize];
	delete[] particleArray;
	particleArray = nullptr;
6.4 TODO 4 - Color Interpolation & blending
TODO 4.1 - Interpolate between start and end color

Create a method that takes to colors and a time step and returns the interpolated SDL_Color.
You can use the pState.pLive.t as the time step.
For color use pState.pLive.startColor and pState.pLive.endColor.
Use the linear interpolation formula: b= a + (b - a) * t
SOLUTION

SDL_Color Particle::RgbInterpolation(SDL_Color startColor, float timeStep, SDL_Color endColor)
{
	SDL_Color finalColor;

	finalColor.r = startColor.r + (endColor.r - startColor.r) * timeStep;
	finalColor.g = startColor.g + (endColor.g - startColor.g) * timeStep;
	finalColor.b = startColor.b + (endColor.b - startColor.b) * timeStep;
	finalColor.a = startColor.a + (endColor.a - startColor.a) * timeStep;

	return finalColor;
}
You should see this on screen:

todo2

TODO 4.2 - Adapt de blit particle method to take blending mode as an argument:

Use SDL_SetTextureColorMod() and SDL_SetTextureAlphaMod() to setup the color.
This has to be done just before calling SDL_RenderCopyEx().
Use the pState.pLive.blendMode and variable.
SOLUTION

	if (SDL_SetTextureColorMod(texture, color.r, color.g, color.b) != 0)
		LOG("Cannot set texture color mode. SDL_SetTextureColorMod error: %s", SDL_GetError());

	if (SDL_SetTextureAlphaMod(texture, color.a) != 0)
		LOG("Cannot set texture alpha mode. SDL_SetTextureAlphaMod error: %s", SDL_GetError());
TODO 4.3 - Adapt de blit particle method to take blending mode as an argument:

Use SDL_SetTextureBlendMode.
As before call it before we the actual render.
Use the pState.pLive.blendMode variable.
SOLUTION

	if (SDL_SetTextureBlendMode(texture, blendMode) != 0)
		LOG("Cannot set texture blend mode. SDL_SetTextureBlendMode error: %s", SDL_GetError());
You should see something like this on screen:

todoblending

6.5 TODO 5 - Fire and Smoke
TODO 5 - Tweak the xml parameters:

Change the emitter data in order to get a flame.
Uncomment code in Scene update to blit the torch.
Optional: create a new one and try simulate smoke.
SOLUTION

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
Now we have a fire!

finan_re

6.6 BONUS CODE - Vortices
Delete the particle movement equations and uncomment this functions to add a vortex!

	// Calculates new particle position.
	CalculateParticlePos(dt);
Now everytime you approach the torch to the top left corner particles will be affected by the turbulence of the vortex like this:

vortextodo

Back to index

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

8. Improvements and further work
This particle system is quite cool but there’s a lot of room for improvement. Here’s a few examples of what can still be done:

Subemitters: create a system within a system. An emitter that spawn other emitters that spawn other emitters and so on. We could use this for fireworks for examples.
Vortex system: create an emitter that instead of spawining particles generate moving vortices that result in moving turbulences.
Multi emitters: create emitters that spawn different type of particles so if we have an explosion this emitter would spawn fire first, a wave and the smoke.
Shapes: instead of limiting emitters to have a single position point they could have shapes to simulate for example a ring of fire.
Animations: instead of rendering a single texture for each particle we could render an animation in loop to simulate more complex effects.
Physics: we’ve seen turbulences with vortices but you can do much more. Paraboles, circular movement different types of turbulences like using the Perlin noise. But there’s much more!
Collisions: you could add colissions between particles to generate more interesting effects.
Other usings: we can use particle systems as line drawings so instead of rendering the particle we render it’s trace. This way we can create organic grass for example. Like this white.sand by Alvy Ray Smith of Lucasfilm:
whitesandslg

These are some suggestions but the possibilities are endless so have fun with them!

Back to index

9. References
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
Back to index

10. License

MIT License

Copyright (c) 2018 nintervik

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

Back to index


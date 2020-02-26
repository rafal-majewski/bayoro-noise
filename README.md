# bayoro-noise
Bayoro noise is a type of noise alternative to perlin, simplex and value noise.
The main difference is that the noise is calculated *in situ* meaning no other points are calculated except the point you want to get height in.

For now, the noise can't be looped in dimensions higher than 1D.


- [License](#License)
- [Theory](#theory)
	- [1D](#theory-1d)
	- [2D](#theory-2d)
- [Implementation](#implementation)
	- [JavaScript](#javascript)
		- [1D](#javascript-1d)
		- [2D](#javascript-2d)

## License
This is free and unencumbered software released into the public domain.

Anyone is free to copy, modify, publish, use, compile, sell, or
distribute this software, either in source code form or as a compiled
binary, for any purpose, commercial or non-commercial, and by any
means.

In jurisdictions that recognize copyright laws, the author or authors
of this software dedicate any and all copyright interest in the
software to the public domain. We make this dedication for the benefit
of the public at large and to the detriment of our heirs and
successors. We intend this dedication to be an overt act of
relinquishment in perpetuity of all present and future rights to this
software under copyright law.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS BE LIABLE FOR ANY CLAIM, DAMAGES OR
OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE,
ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR
OTHER DEALINGS IN THE SOFTWARE.

For more information, please refer to <http://unlicense.org/>

## Theory
The algorithm basically calculates a sum of scaled, rotated and shifted sinuses (called *coefficients* throughout the documentation), that are divided into layers.

Inspired by [the Weierstrass function](https://en.wikipedia.org/wiki/Weierstrass_function) and [the Fourier transform](https://en.wikipedia.org/wiki/Fourier_transform).
### 1D
#### Formula:

![Formula for 1D](1d_formula.png)

where:
`n` is the number of layers

`amplitude[i]` is the amplitude of i-th sinus (should decrease geometrically with each layer)

`shift[i]` is the shift of i-th sinus (has to be between 0 and 2π)

`frequency[i]` is the frequency of i-th sinus (should increase geometrically with each layer)


The generated noise will loop if all frequencies are multiples of π and the maximal value of `f(x)` is the sum of all amplitudes.
#### Visual example:
|Layer 0|Layer 1|Layer 2|Sum|
|-------|-------|-------|---|
|amplitude: 1<br>frequency: 1|amplitude: 0.5<br>frequency: 2|amplitude: 0.25<br>frequency: 4||
|![Layer 0](1d_layer_0.png)|![Layer 1](1d_layer_1.png)|![Layer 2](1d_layer_2.png)|![Sum](1d_sum.png)|

#### Pseudocode example:
```
let coefficients=[
	{
		shift: 2*PI*random(),
		amplitude: 1,
		frequency: 1
	},
	{
		shift: 2*PI*random(),
		amplitude: 0.5,
		frequency: 2
	},
	{
		shift: 2*PI*random(),
		amplitude: 0.25,
		frequency: 4
	},
];

function noise(x) {
	let sum=0; // variable to store the sum
	// iterate over all coefficients
	for (let i=0; i<n; ++i) {
		let coef=coefficients[i];
		sum+=coef.amplitude*sin(coef.shift+x*coef.frequency);
	}
	return sum;
}
```
### 2D
#### Formula:
The formula is pretty similar to its 1D version, but in order to hide vertical and horizontal lines layers have to be rotated and there should be multiple layers with the same frequency but different shifts and rotations.

![Formula for 2D](2d_formula.png)

where:
`n` is the number of layers

`x'` is the x-part of `(x, y)` rotated around `(0, 0)` by a random angle stored in current layer

`y'` is the y-part of `(x, y)` rotated around `(0, 0)` by a random angle stored in current layer

`amplitude[i]` is the amplitude of i-th sinus (should decrease geometrically with each layer)

`shift[ix]` is the x-shift of i-th sinus (has to be between 0 and 2π)

`shift[iy]` is the y-shift of i-th sinus (has to be between 0 and 2π)

`frequency[i]` is the frequency of i-th sinus (should increase geometrically with each layer)

Since every layer is randomly rotated the noise can't be looped.

#### Pseudocode example:
```
let coefficients=[
	{
		shiftx: 2*PI*random(),
		shifty: 2*PI*random(),
		angle: 2*PI*random(),
		amplitude: 1,
		frequency: 1
	},
	{
		shiftx: 2*PI*random(),
		shifty: 2*PI*random(),
		angle: 2*PI*random(),
		amplitude: 1,
		frequency: 1
	},
	{
		shiftx: 2*PI*random(),
		shifty: 2*PI*random(),
		angle: 2*PI*random(),
		amplitude: 1,
		frequency: 1
	},
	{
		shiftx: 2*PI*random(),
		shifty: 2*PI*random(),
		angle: 2*PI*random(),
		amplitude: 1,
		frequency: 1
	},
	{
		shiftx: 2*PI*random(),
		shifty: 2*PI*random(),
		angle: 2*PI*random(),
		amplitude: 0.5,
		frequency: 2
	},
	{
		shiftx: 2*PI*random(),
		shifty: 2*PI*random(),
		angle: 2*PI*random(),
		amplitude: 0.5,
		frequency: 2
	},
	{
		shiftx: 2*PI*random(),
		shifty: 2*PI*random(),
		angle: 2*PI*random(),
		amplitude: 0.5,
		frequency: 2
	},
	{
		shiftx: 2*PI*random(),
		shifty: 2*PI*random(),
		angle: 2*PI*random(),
		amplitude: 0.5,
		frequency: 2
	},
	{
		shiftx: 2*PI*random(),
		shifty: 2*PI*random(),
		angle: 2*PI*random(),
		amplitude: 0.25,
		frequency: 4
	},
	{
		shiftx: 2*PI*random(),
		shifty: 2*PI*random(),
		angle: 2*PI*random(),
		amplitude: 0.25,
		frequency: 4
	},
	{
		shiftx: 2*PI*random(),
		shifty: 2*PI*random(),
		angle: 2*PI*random(),
		amplitude: 0.25,
		frequency: 4
	},
	{
		shiftx: 2*PI*random(),
		shifty: 2*PI*random(),
		angle: 2*PI*random(),
		amplitude: 0.25,
		frequency: 4
	},
	{
		shiftx: 2*PI*random(),
		shifty: 2*PI*random(),
		angle: 2*PI*random(),
		amplitude: 0.125,
		frequency: 8
	},
	{
		shiftx: 2*PI*random(),
		shifty: 2*PI*random(),
		angle: 2*PI*random(),
		amplitude: 0.125,
		frequency: 8
	},
	{
		shiftx: 2*PI*random(),
		shifty: 2*PI*random(),
		angle: 2*PI*random(),
		amplitude: 0.125,
		frequency: 8
	},
	{
		shiftx: 2*PI*random(),
		shifty: 2*PI*random(),
		angle: 2*PI*random(),
		amplitude: 0.125,
		frequency: 8
	},
	{
		shiftx: 2*PI*random(),
		shifty: 2*PI*random(),
		angle: 2*PI*random(),
		amplitude: 0.125,
		frequency: 8
	},
];

function noise(x) {
	let sum=0; // variable to store the sum
	// iterate over all coefficients
	for (let i=0; i<n; ++i) {
		let coef=coefficients[i];
		// rotation formula can be found on Wikipedia - https://en.wikipedia.org/wiki/Rotation_matrix#In_two_dimensions
		sum+=coef.amplitude*sin(coef.shift+(Math.cos(coef.angle)*x-Math.sin(coef.angle)*y)*coef.frequency)*sin(coef.shift+(Math.sin(coef.angle)*x+Math.cos(coef.angle)*y)*coef.frequency);
	}
	return sum;
}
```
![Example noise](2d_example.png)
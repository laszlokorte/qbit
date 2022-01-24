<script>
	const formatter = new Intl.NumberFormat('en-IN', { maximumFractionDigits: 3, minimumFractionDigits: 3 })
	
	let normalizeAngle = false
	let radA = 0
	let radB = 0
	
	let magA = 1/Math.sqrt(2)
	let magB = Math.sqrt(1 - magA * magA)
	$: angleA = normalizeAngle ? 0 : radA * 2 * Math.PI
	$: angleB = radB * 2 * Math.PI - (normalizeAngle ? (magA !== 0 ? radA : radB) * 2 * Math.PI : 0)
	
	$: reA = Math.cos(angleA) * magA
	$: imA = Math.sin(angleA) * magA
	
	$: reB = Math.cos(angleB) * magB
	$: imB = Math.sin(angleB) * magB
	
	function setMagA(mag) {
		magA = mag
		magB = Math.sqrt(1 - mag * mag)
	}
	function setMagB(mag) {
		magB = mag
		magA = Math.sqrt(1 - mag * mag)
	}
</script>

<style>
	* {
		box-sizing: border-box;
	}

	dl {
		display: grid;
		grid-template-columns: max-content max-content max-content;
		gap: 0 1em;
		align-items: center;
		margin: 0;
	}
	
	dd {
		margin: 0;
		padding: 0;
	}
	
	input{
		margin: 0;
		padding: 0.5em;
	}
	
	figure {
		display: grid;
		grid-template-columns: [all-start q2-start q3-start] minmax(10em, 1fr) [q3-end q2-end q1-start q4-start] minmax(10em, 1fr) [a4-end q1-end all-end];
		grid-template-rows: [all-start q1-start q2-start] minmax(10em, 1fr) [q1-end q2-end q3-start q4-start] minmax(10em, 1fr) [all-end q3-end q4-end];
		gap: 5em;
	}
	
	.coords {
		grid-area: all;
	}
	
	fieldset {
		grid-area: q2;
		align-self: start;
		justify-self: start;
		z-index: 2;
		background: #fffa;
		display: grid;
		grid-template-columns: [full-start] repeat(auto-fit, minmax(20em, 1fr)) [full-end];
		grid-template-rows: [full-start] auto [full-end];
		grid-auto-rows: auto;
		padding: 1em 2em;
	}

	.circles {
		width: 100%;
		display: block;
	}

	figure {
		margin: 0;
		display: block;
		padding: 1em;
	}

	legend {
		grid-area: full;
	}
	
	
	h1 {
		margin: 0
	}

	article {
		max-width: 60em;
		margin:  auto;
	}
</style>
<article>
	
<h1>
	Q-bit representation
</h1>

<p>
	This illustration shows my current unterstanding of qbits based on <a href="https://www.youtube.com/watch?v=JWf_g_ForGk">this video from Prof. Dr. Edmund Weitz</a>.
</p>

<p>
	A qbit is represented as two complex numbers. A constraint is that the squares of their magnitudes sum to 1. That is because their magnitude square represent the probability of the qbit eventually being measured as a 0 or a 1. So both probabilities together must sum to 1.
</p>
<p>
	Physically the individual phases of the complex numbers can not be distinguished, only their relative offset. So typically the angle of one complex number is normalized to be 0 and the other is shifted accordingly.
</p>
<p>
	The constraint to sum to 1 and the normalization of the angles reduce the degrees of freedom from 4 down to 2. So in princible a qbit can be represented by a single complex number that magnituede is less than or equal to 1.
</p>
<p>
	Quantum gates can be used to transform such a qbit in differnt ways. Obviously only transformations that do not violate the stated contraints are allowed. Such Quantum gates can be composed into a circuit. The input and output of the circuit are qbits. But only a limited set of qbit configurations can be created as input and only qbits with |&alpha|=1 or |&beta|=1 can be measured as output. An output must be measured/sampled multiple times to estimate the true magnitude value.
</p>

<fieldset>
		<legend>
			Controls
		</legend>

		<dl>
			<dt><label for="maga">Magnitude &alpha;</label></dt>
			<dd><input style="accent-color: red;" id="maga" type="range" value={magA} on:input={(evt) => setMagA(evt.currentTarget.valueAsNumber)} min="0" max="1" step="0.001" /></dd>
			<dd><output>{formatter.format(magA)}</output></dd>
			
			<dt><label for="anglea">Angle &alpha;</label></dt>
			<dd><input style="accent-color: red;" id="anglea" type="range" bind:value={radA} min={0} max={1} step="0.001" /></dd>
			<dd><output>{formatter.format(radA)}</output></dd>

			<dt><label for="maga">Magnitude &beta;</label></dt>
			<dd><input style="accent-color: blue;" id="maga" type="range" value={magB} on:input={(evt) => setMagB(evt.currentTarget.valueAsNumber)} min="0" max="1" step="0.001" /></dd>
			<dd><output>{formatter.format(magB)}</output></dd>
			
			<dt><label for="angleb">Angle &beta;</label></dt>
			<dd><input style="accent-color: blue;" id="angleb" type="range" bind:value={radB} min={0} max={1} step="0.001" /></dd>
			<dd><output>{formatter.format(radB)}</output></dd>
			
			<dt></dt>
			<dd><label><input type="checkbox" bind:checked={normalizeAngle} /> Normalize Angle A to Zero</label></dd>
		</dl>

		
	
	<figure>
		<svg viewBox="-100 -50 200 100" class="circles">
			<circle cx="-50" cy="0" r="40" fill="#eee" stroke="gray" vector-effect="non-scaling-stroke" />
			<circle cx="50" cy="0" r="40" fill="#eee" stroke="gray" vector-effect="non-scaling-stroke" />
			<circle opacity="0.5" cx="-50" cy="0" r={magA*40} fill="red" />
			<circle opacity="0.5" cx="50" cy="0" r={magB*40} fill="blue" />
				
				{#if magA !==0 && (magA !== 1 || !normalizeAngle)}
				
				<polyline stroke-width="2" vector-effect="non-scaling-stroke" points={[-50, 0, -50 + 40 * Math.cos(angleA), -40 * Math.sin(angleA)].join(' ')} stroke="red" />
					{/if}
				{#if magB !==0 && (magB !== 1 || !normalizeAngle)}
				<polyline stroke-width="2" vector-effect="non-scaling-stroke" points={[50, 0, 50 + 40 * Math.cos(angleB), -40 * Math.sin(angleB)].join(' ')} stroke="blue" />
				{/if}
				<text x="-50" y="50" text-anchor="middle" font-size="10">&alpha;</text>
				<text x="50" y="50" text-anchor="middle" font-size="10">&beta;</text>
			</svg>
			
		</figure>
	</fieldset>

<figure>
	<svg class="coords" viewBox="-100 -60 200 120">
		<g stroke-width="1" font-size="4">
			<polyline points="-98 0 98 0" stroke="black"  vector-effect="non-scaling-stroke" />
			<polyline points="0 -50 0 50" stroke="black"  vector-effect="non-scaling-stroke" />
			<polygon points="100 0 98 1 98 -1" />
			<polygon points="0 -50 1 -48 -1 -48" />
			<text x="2" y="-50" dominant-baseline="hanging">Im</text>
			<text x="97" y="-2" text-anchor="end">Re</text>
		</g>

		
		<g transform={`rotate(${-angleA*180 / Math.PI})`}>
		<polyline stroke-width="2" vector-effect="non-scaling-stroke" points={[0, 0, 50 * magA - 2, 0].join(' ')} stroke="red" />

			<polygon points="{magA*50} 0 {magA*50-2} 1 {magA*50-2} -1 " fill="red" />
			
		<rect x="0" y="0" width={magA*50} height={magA*50} fill="red" opacity="0.1" />
		</g>
		
		<g transform={`rotate(${-angleB*180 / Math.PI})`}>
		<polyline stroke-width="2" vector-effect="non-scaling-stroke" points={[0, 0, 50 * magB - 2, 0].join(' ')} stroke="blue" />
						<polygon points="{magB*50} 0 {magB*50-2} 1 {magB*50-2} -1 " fill="blue" />

			
		<rect x="0" y={-magB*50} width={magB*50} height={magB*50} fill="blue" opacity="0.1" />
			
			</g>
		
		<g font-size="3">
		
					<text x={reA*25 +imA*25} y={imA*-25 + reA*25} text-anchor="middle" dominant-baseline="middle">P(q=1) =  |&alpha;|&#xB2;</text>		
					<text x={reB*25 -imB*25} y={imB*-25 - reB*25} text-anchor="middle" dominant-baseline="middle">P(q=0) =  |&beta;|&#xB2;</text>
			
					<text x={reA*45 -imA*3} y={imA*-45 - reA*3} text-anchor="middle" dominant-baseline="middle">&alpha;</text>		
					<text x={reB*45 +imB*3} y={imB*-45+ reB*3} text-anchor="middle" dominant-baseline="middle">&beta;</text>
		</g>



	</svg>
</figure>
</article>
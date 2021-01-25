<script>
	let text = "";
	let short = "";
	let warning_msg = "";
	let output = "";
	$: l = text.length;

	const data = () => {
		text = "";

		warning.setAttribute("hidden", "");
		loading.removeAttribute("hidden");

		fetch("/short/" + short)
			.then((res) => res.json())
			.then((data) => {
				loading.setAttribute("hidden", "");

				if (data.message == "") {
					warning.removeAttribute("hidden");
					warning_msg = data.message;
				} else {
					text = data.message;
				}
			})
			.catch(() => loading.setAttribute("hidden", ""));
	};

	const upload = () => {
		output = "";

		if (text.length > 256) {
			warning = "Text length exceeded limit of 256";
			return;
		}

		shortoutput.setAttribute("hidden", "");
		outputloader.removeAttribute("hidden");

		fetch("/data/", {
			method: "POST",
			headers: {
				"Content-Type": "application/json",
			},
			body: JSON.stringify({ text: text }),
		})
			.then((res) => res.json())
			.then((data) => {
				output = data.message;
				shortoutput.removeAttribute("hidden");
				outputloader.setAttribute("hidden", "");
			})
			.catch(() => {
				outputloader.setAttribute("hidden", "");
				shortoutput.setAttribute("hidden", "");
			});
	};

	const copyToClipboard = () => {
		const app = new CopyClipBoard({
			target: document.getElementById("clipboard"),
			props: { output },
		});
		app.$destroy();
		document.getElementById("clipbutton").innerHTML = "copied";
	};
</script>

<div class="container">
	<form autocomplete="off">
		<label for="shortcode">Enter identifier</label>
		<div class="input-group">
			<input
				type="text"
				placeholder="foobar"
				class="form-control"
				bind:value={short}
				id="shortcode"
			/>

			<div hidden id="loading" class="icon-container">
				<i class="loader" />
			</div>

			<span class="input-group-btn">
				<button class="btn btn-primary" type="button" on:click={data}>Find Paste</button>
			</span>
		</div>

		<center><strong>OR</strong></center>

		<div class="form-group">
			<label for="text">Paste something</label>
			<textarea
				class="form-control"
				id="text"
				rows="6"
				cols="30"
				bind:value={text}
			/>
			<p>{l} chars. {l > 256 ? "char limit exceeded(256)" : ""}</p>
		</div>

		<button	type="button" on:click={upload}>Share</button>
	</form>

	<br /><br />

	<center>
		<div hidden id="shortoutput">
			pasteit code:
			{output}
		</div>
	</center>
</div>

<div id="clipboard" />

<style>
</style>

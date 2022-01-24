<script lang="ts">
	// Import required Font Awesome icons
	import Fa from 'svelte-fa';
	import { faGithub } from '@fortawesome/free-brands-svg-icons';
	
	import '../app.css';
</script>

<div class="flex items-center justify-center h-screen">
	<slot />

	<div class="absolute bottom-14">
		<a href="https://github.com/newtykins/calculator"><Fa icon={faGithub} size="3x" /></a>
	</div>
</div>

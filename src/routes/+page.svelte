<script lang="ts">
	import { Spring } from 'svelte/motion';

	/* * Selected tab index; drives bump visibility and tab styling */
	let selectedIndex = $state(0);

	/* * Physics-based spring: fluid motion with a gentle bounce at the end (lower damping = overshoot) */
	const bumpScale0 = new Spring(1, { stiffness: 0.21, damping: 0.44 });
	const bumpScale1 = new Spring(0, { stiffness: 0.21, damping: 0.44 });

	function selectTab(index: number) {
		if (index === selectedIndex) return;
		(index === 0 ? bumpScale1 : bumpScale0).set(0);
		selectedIndex = index;
		(selectedIndex === 0 ? bumpScale0 : bumpScale1).set(1);
	}

	const tabs = [
		{ id: 0, label: 'MenuItem' },
		{ id: 1, label: 'MenuItem' }
	];
</script>

<div class="page">
	<div class="menu-card">
		<div class="tabs-row">
			{#each tabs as tab}
				<div class="tab-slot">
					<button
						class="tab"
						class:selected={selectedIndex === tab.id}
						role="tab"
						aria-selected={selectedIndex === tab.id}
						onclick={() => selectTab(tab.id)}
					>
						{tab.label}
					</button>
					<div
						class="bump-wrapper"
						aria-hidden="true"
					>
						<div
							class="bump"
							style="transform: translateX(-50%) scaleY({tab.id === 0 ? bumpScale0.current : bumpScale1.current})"
						>
							<svg class="bump-svg" xmlns="http://www.w3.org/2000/svg" viewBox="-9 -3 18 3">
								<path d="M 9 0 C 4 -1 4 -3 0 -3 S -3 -1 -9 0 z" />
							</svg>
						</div>
					</div>
				</div>
			{/each}
		</div>
		<div class="white-section">
			<div class="white-panel"></div>
		</div>
	</div>
</div>

<style>
	.page {
		display: flex;
		justify-content: center;
		align-items: center;
		min-height: 100vh;
		padding: 2rem;
	}

	.menu-card {
		display: flex;
		flex-direction: column;
		min-height: 400px;
		min-width: 800px;
		max-width: 480px;
		width: 100%;
		background-color: var(--card-obsidian);
		border: 1px solid var(--card-border);
		border-radius: var(--border-radius);
	}

	.tabs-row {
		display: flex;
		justify-content: flex-end;
		gap: 0.5rem;
		flex-shrink: 0;
		padding-inline: 2rem;
	}

	.tab-slot {
		display: flex;
		flex-direction: column;
		align-items: center;
	}

	.tab {
		height: 90px;
		width: 120px;
		background: none;
		border: none;
		color: var(--text-muted);
		font-size: 1rem;
		cursor: pointer;
		border-radius: 8px;
		transition: color 0.2s ease;
	}

	.tab:hover {
		color: var(--white);
	}

	.tab.selected {
		color: var(--accent-blue);
	}

	/* * Bump sits at bottom of tab-slot and overlaps the white section */
	.bump-wrapper {
		width: 100%;
		position: relative;
		display: flex;
		justify-content: center;
	}

	/* * Bump: smooth curve protruding from top of panel (shallower height) */
	.bump {
		position: absolute;
		bottom: 0;
		left: 50%;
		width: 90px;
		transform-origin: center bottom;
	}

	.white-section {
		flex: 1;
		min-height: 0;
		position: relative;
		background-color: var(--panel-bg);
		border-radius: var(--border-radius);
	}

	.bump-svg {
		width: 100%;
		height: 100%;
		display: block;
		fill: var(--panel-bg);
	}

	.white-panel {
		flex: 1;
		background-color: var(--panel-bg);
		border-radius: 16px;
		min-height: 0;
	}
</style>

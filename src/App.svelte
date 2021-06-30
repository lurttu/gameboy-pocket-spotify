<script>
	import Icon from 'svelte-awesome';
	import { pause,play } from 'svelte-awesome/icons';
	let pressed = '';
	let now_playing;
	let is_paused = false;
	let show_menu = false;

	let menu_items = ['playlists', 'now playing', 'settings'];

	let selected_item = menu_items[0];

	setInterval(() => {
		getState()
	}, 3000);

	const handleClick = (button) => {
		console.log(window.player)
        window.player.togglePlay().then(() => {
          getState();
        });
	}

	const getState = () => {
		player.getCurrentState().then(state => {
  			if (!state) {
    			console.error('User is not playing music through the Web Playback SDK');
    			return;
  			}

			let {
				current_track,
				next_tracks: [next_track]
			} = state.track_window;
  			now_playing = current_track;
			if (state.paused) {
				is_paused = true;
		  	} else {
			  is_paused = false;
		  	}
		});
	}
	const toggleMenu = () => {
		show_menu = !show_menu;
	}

	const navigate = (direction) => {
		if (show_menu) {
			switch (direction) {
				case 'up':
					if (menu_items.indexOf(selected_item) === 0) {
						selected_item = menu_items[menu_items.length - 1]
					return;
					}
					if (menu_items.indexOf(selected_item) > 0) {
						selected_item = menu_items[menu_items.indexOf(selected_item) - 1]
						return;
					}
					break;
				case 'down':
					if (menu_items.indexOf(selected_item) === menu_items.length - 1) {
						selected_item = menu_items[0]
					return;
					}
					if (menu_items.indexOf(selected_item) < menu_items.length) {
						selected_item = menu_items[menu_items.indexOf(selected_item) + 1]
						return;
					}
					break;
				default:
					console.error('navigation error');
			}
		}
		else return;
	}

	const changeTrack = (direction) => {
		if (!show_menu) {
			switch (direction) {
				case 'left':
					window.player.previousTrack().then(() => {
						console.log('Set to previous track!');
					});
					break;
				case 'right':
					window.player.nextTrack().then(() => {
						console.log('Set to next track!');
					});
					break;
				default:
					console.error('error while changing track');
			}
		}
	}
</script>

<main>
	<div class="body">
		<div class="screen">
		  <div class="inner">
			{#if !show_menu}
				{#if now_playing}
				<p>{now_playing.artists[0].name + ' ' + now_playing.name}</p>
				<p>{#if is_paused}<Icon data={pause} /> {:else}<Icon data={play} />{/if}</p>
				{:else}
				Nintendo®
				{/if}
			{/if}
			{#if show_menu}
				<div class="menu">
					{#each menu_items as item}
					<div class="{selected_item === item ? 'menu-item selected' : 'menu-item'}">{item}</div>
					{/each}
				</div>
			{/if}
		  </div>
		</div>
		<div class="buttons">
		  <div class="upper-row">
			<div class="dpad">
			  <div class="x">
				<div class="left" on:click="{() => changeTrack('left')}"></div>
				<div class="right" on:click="{() => changeTrack('right')}"></div>
			  </div>
			  <div class="y">
				<div class="up" on:click="{() => navigate('up')}"></div>
				<div class="down" on:click="{() => navigate('down')}"></div>
			  </div>
			</div>
			<div class="ab">
			  <div class="button b" on:click="{() => handleClick('b')}"></div>
			  <div class="button a"></div>
			</div>
		  </div>
		  <div class="lower-row">
			<div class="button"></div>
			<div class="button" on:click="{toggleMenu}"></div>
		  </div>
		</div>
		<div class="speaker"></div>
	  </div>
</main>

<style lang="scss">
	.body {
  margin: 30px;
  position: relative;
  background: #dcd0ff;
  width: 250px;
  height: 400px;
  border-radius: 8px 8px 50px 8px;
  display: flex;
  flex-direction: column;
  align-items: center;
  box-shadow: 0 6px 4px 0 rgba(0,0,0,0.14), 0 1px 18px 0 rgba(0,0,0,0.12), 6px 3px 10px -1px rgba(0,0,0,0.20);
  margin: auto;
  margin-top: 200px;
}

.screen {
  display: flex;
  flex-direction: column;
  align-items: center;
  height: 50%;
  width: 90%;
  background: #000;
  margin-top: 20px;
  border-radius: 8px 8px 30px 8px;
  .inner {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    text-align: center;
    font-family: monotype;
    margin-top: 20px;
    height: 80%;
    width: 75%;
    background: #B7CDA1;
    font-size: 20px;
    font-family: monospace;
    font-weight: 600;
    letter-spacing: 0.001em;
	.menu {
		display:flex;
		flex-direction: column;
		justify-content: center;
		width: 100%;
		height: 100%;
		.menu-item {
			width: 100%;
			height: auto;
			&.selected {
				background: #000;
				color: #B7CDA1;
			}
		}
	}
  }
}

.buttons {
  display: flex;
  flex-direction: column;
  margin-top: 30px;
  width: 85%;
  .upper-row {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    .dpad {
      position: relative;
      width: 60px;
      height: 60px;
      .x {
        position: absolute;
        top: 20px;
		display: flex;
		justify-content: space-between;
		flex-direction: row;
        height: calc(100% / 3);
        width: 100%;
        background: #000;
        border-radius: 3px;
        box-shadow: 0 2px 2px 0 rgba(0,0,0,0.14), 0 3px 1px -2px rgba(0,0,0,0.12), 0 1px 5px 0 rgba(0,0,0,0.20);
		.left, .right {
			width: calc(100% / 3);
			height: 100%;
			cursor: pointer;
		}
      }
      .y {
        position: absolute;
        left: 20px;
		display: flex;
		flex-direction: column;
		justify-content: space-between;
        height: 100%;
        width: calc(100% / 3);
        background: #000;
        border-radius: 3px;
        box-shadow: 0 2px 2px 0 rgba(0,0,0,0.14), 0 3px 1px -2px rgba(0,0,0,0.12), 0 1px 5px 0 rgba(0,0,0,0.20);
		.up, .down {
			height: calc(100% / 3);
			width: 100%;
			cursor: pointer;
		}
      }
    }
    .ab {
      position: relative;
      width: 80px;
      .button {
        width: 30px;
        height: 30px;
        border-radius: 50%;
        background: #000;
        box-shadow: 0 2px 2px 0 rgba(0,0,0,0.14), 0 3px 1px -2px rgba(0,0,0,0.12), 0 1px 5px 0 rgba(0,0,0,0.20);
        &.a {
          position: absolute;
          bottom: 5px;
          left: 5px;
        }
        &.b {
          position: absolute;
          top: 5px;
          right: 5px;
		  cursor: pointer;
        }
      }
    }
  }
  .lower-row {
    margin-top: 30px;
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
    height: 30px;
    gap: 7px;
    .button {
      width: 27px;
      height: 8px;
      border-radius: 10px;
      background: #000;
      box-shadow: 0 2px 2px 0 rgba(0,0,0,0.14), 0 3px 1px -2px rgba(0,0,0,0.12), 0 1px 5px 0 rgba(0,0,0,0.20);
    }
  }
}

.speaker {
  position: absolute;
  width: 50px;
  height: 50px;
  bottom: 15px;
  right: 15px;
  border-radius: 50% 0 50% 0;
  background-color: #dcd0ff;
background-image: radial-gradient(black 15%, transparent 16%),
radial-gradient(#666 15%, transparent 16%);
background-size: 10px 10px;
background-position: 0 0, 15px 35px;
}
</style>
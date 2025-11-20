<script>
    import Buttons from '../lib/_buttons.svelte';
    import { onMount } from 'svelte';

    let spotifyToken = '';
    let track = null;
    let error = '';
    let polling = null;

    async function fetchCurrentlyPlaying() {
        error = '';
        if (!spotifyToken) {
            track = null;
            return;
        }
        try {
            const res = await fetch('https://api.spotify.com/v1/me/player/currently-playing', {
                headers: { Authorization: `Bearer ${spotifyToken}` }
            });
            if (res.status === 204) {
                track = null;
                return;
            }
            if (res.status === 401) {
                error = 'Spotify token expired or invalid. Please update token.';
                track = null;
                return;
            }
            if (!res.ok) {
                error = `Spotify API error: ${res.status}`;
                track = null;
                return;
            }
            const data = await res.json();
            if (data && data.item) {
                track = {
                    name: data.item.name,
                    artists: data.item.artists.map(a => a.name).join(', '),
                    id: data.item.id,
                    url: data.item.external_urls?.spotify,
                    is_playing: data.is_playing
                };
            } else {
                track = null;
            }
        } catch (e) {
            error = 'Network error fetching Spotify data';
            track = null;
        }
    }

    function saveToken() {
        if (typeof localStorage !== 'undefined') localStorage.setItem('spotify_token', spotifyToken);
        fetchCurrentlyPlaying();
        if (!polling) polling = setInterval(fetchCurrentlyPlaying, 15000);
    }

    function clearToken() {
        spotifyToken = '';
        if (typeof localStorage !== 'undefined') localStorage.removeItem('spotify_token');
        track = null;
        error = '';
        if (polling) { clearInterval(polling); polling = null; }
    }

    onMount(() => {
        if (typeof localStorage !== 'undefined') {
            spotifyToken = localStorage.getItem('spotify_token') || '';
        }
        if (spotifyToken) {
            fetchCurrentlyPlaying();
            polling = setInterval(fetchCurrentlyPlaying, 15000);
        }
        return () => { if (polling) clearInterval(polling); };
    });
</script>

<header>
    <h1>Franciszek Czajkowski</h1>
    <div class="header_info">
        <p>
            UI/UX Designer & Fullstack Developer
        </p>
        <p>Warsaw Poland</p>
    </div>
    <p>
        Currently, I am a third-year student at a technical high school, <br> specializing in the field of IT as a software technician. Outside of school,<br> I am independently learning Rust to deepen my programming skills.
    </p>
    <Buttons />

    <div class="aboutInfo">

        <p>
            {#if track}
                <a href={track.url} target="_blank" rel="noopener noreferrer">Now playing: {track.name} — {track.artists}</a>
            {:else}
                {#if spotifyToken}
                    <span>Nothing is playing right now.</span>
                {:else}
                    <span>No Spotify token. Paste an access token below to show your current track.</span>
                {/if}
            {/if}
        </p>
        <p>
            No recent Game
        </p>
        
    </div>
</header>
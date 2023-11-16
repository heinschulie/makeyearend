<script lang="ts">
	import { page } from "$app/stores";

	
    // Import the functions you need from the SDKs you need
    import { initializeApp } from "firebase/app";
    import { getFirestore, collection, doc, onSnapshot, setDoc, query } from "firebase/firestore";
    // TODO: Add SDKs for Firebase products that you want to use
    // https://firebase.google.com/docs/web/setup#available-libraries

    // Your web app's Firebase configuration
    const firebaseConfig = {
        apiKey: "AIzaSyDEbIiQFmhASV9yWZuw9HPvuW_CHdQLVdM",
        authDomain: "makestudioct.firebaseapp.com",
        projectId: "makestudioct",
        storageBucket: "makestudioct.appspot.com",
        messagingSenderId: "714732421526",
        appId: "1:714732421526:web:b20d7c71d7ac223b601db7"
    };

    // Initialize Firebase
    const app = initializeApp(firebaseConfig);
    const db = getFirestore(app);

    const membersCollectionRef = collection(db, "members"); 
    const q = query(membersCollectionRef);
    const unsub = onSnapshot(q, (querySnapshot) => {
        members = []; 
        querySnapshot.forEach((doc) => {
            members = [ ...members, doc.data() as { name: string, yearend: boolean } ];
        });
    });
    
    
    import { onDestroy, onMount } from "svelte";
    import { fade } from 'svelte/transition';

    $: members = [] as { name: string, yearend: boolean }[]; 

    const user = $page.url.searchParams.get("iam"); 

    let showTitle = false; 
    let video: HTMLVideoElement; 
    let audio: HTMLAudioElement; 
    let playbackTime: number = 0; 
    let playbackRate = 1.10;
    let topSpeedReached = false; 

    onMount(() => {
        console.log("VIDEO: ", video);
        console.log("AUDIO: ", audio);
    })

    const play = () => {
        video.play();
        audio.play();
        // showTitle = true;
    }
    $: if(!topSpeedReached && playbackTime > 5) {
        topSpeedReached = true;
        // playbackRate = 1.45;
        playbackRate = 1.5;
    }
    $: if(!!video && playbackTime > 54.5) {
        video.pause();
        console.log("PAUSED AT: ", playbackTime);
        showTitle = true; 
    }

    const rsvp = async (memberName: string, currentStatus: boolean) => {
        if(user === memberName) {
            await setDoc(doc(membersCollectionRef, memberName), {
                name: memberName,
                yearend: !currentStatus
            });
        }   
    }

    onDestroy(() => {
        unsub(); 
    })
    
</script>
<svelte:head>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin={'true'}>
    <link href="https://fonts.googleapis.com/css2?family=Comfortaa:wght@700&family=Dancing+Script:wght@600&family=Josefin+Sans:wght@300&family=Raleway:wght@300&display=swap;400&display=swap" rel="stylesheet">
</svelte:head>
<!-- <h1>PLAYING: { playbackTime }</h1> -->
<button class="mainButton" on:click={play}>PLAY</button>
<video 
    autoplay={false}
    bind:playbackRate={playbackRate} 
    bind:currentTime={playbackTime} 
    bind:this={video} 
    muted
    src="/stock-footage-a-retro-flip-calendar-showing-every-day-of-up-until-january-st.webm"></video>
<audio
    autoplay={false} 
    bind:this={audio}>
    <source src="/The Black Keys  These Days  Remastered 10th Anniversary Edition [Official Audio].mp3" type="audio/mpeg">
    Your browser does not support the audio tag.
</audio>

{#if showTitle}
<h1 class="title">
    <span in:fade={{ delay: 2000, duration: 5000 }}>This year is </span><span in:fade={{ delay: 5000, duration: 5000 }}> finally </span><span in:fade={{ delay: 7000, duration: 5000 }}> fucking </span><span in:fade={{ delay: 9000, duration: 5000 }}> ending.</span>
    <span in:fade={{ delay: 12000, duration: 5000 }}>Let's drink to its demise.</span>
</h1>
<!-- {/if} -->

<div class="pics" in:fade={{ delay: 18000, duration: 3000 }}>
    {#each members as member}
    <button on:click={() => rsvp(member.name, member.yearend)}>
        <img class="pic {member.yearend ? 'yes' : 'no'}" src="/{member.name}.jpeg" alt="a photo of {member.name}" style="transform: rotate({Math.random()*10*(Math.random() < 0.5 ? -1 : 1)}deg)"/>
    </button>
    {/each}
</div>

<!-- {#if showTitle} -->
<h2 in:fade={{ delay: 15000, duration: 3000 }}>Pick up at office at 9AM. Day in winelands. Lunch. Home at 5:30pm. Click on your photo if you're in.</h2>
{/if}
 
<style>

    .mainButton {
        border: 2px solid cyan;
        color: white; 
        padding: 2vw;
        border-radius: 15px;
    }
    :global(body) { 
        background-color: #202425;
    }
    .title {
        float: left;
        float: left;
        padding: 0 50px;
    }
    h1{
        font-family: 'Dancing Script', cursive;
        font-size: 50px;
        display: inline-block;
        margin: 0;
    }
    span:first-child{
        color: yellow;
    }
    span:nth-child(2), span:nth-child(3), span:nth-child(4) {
        color: white;
    }
    span:nth-child(5) {
        color: magenta;
    }
    .title, h2, button {
        position: relative;
        z-index: 1000;
    }
    h2 {
        /* position: absolute;
        bottom: 50px;
        left: 50px;*/
        padding: 0 5vw;
        font-size: 16px;
        font-family: 'Raleway', sans-serif;
        color: aquamarine;  
        text-align: justify;
    }
    video {
        z-index: -1;
        position: absolute;
        top: 0;
        left: 0;
        display: block;
        width: 100%;
        height: 100%;
        object-fit: contain;
    }
    .pics {
        position: absolute;
        bottom: 10vw;
        width: 90vw;
        left: 5vw;
    }
    .pic {
        width: 8vh;
        height: 8vh;
        border: 4px solid cadetblue;
        
        transition: 0.2s;
    }
    .pic:hover {
        filter: grayscale(0);
        cursor: pointer;
    }

    .yes {
        filter: grayscale(0);
    }
    .no {
        filter: grayscale(100);
    }
    button {
        background-color: transparent;
        outline: none;
        border: none;
    }


    @media screen and (min-width: 600px) {

        .mainButton {
            border: 2px solid cyan;
            color: white; 
            padding: 1vw;
            border-radius: 15px;
        }
        
        video {
            object-fit: cover;
        }
        .pics {
            position: absolute; 
            width: 80vw;
            bottom: unset;
            top: 65vh;
            left: 10vw;

            display: grid;
            grid-auto-flow: column;
            gap: 5px;
            place-content: center;
        }
        h1{
            font-family: 'Dancing Script', cursive;
            font-size: 100px;
            display: inline-block;
            margin: 0;
        }
        h2 {
            position: absolute;
            bottom: 50px;
            left: 50px;
            font-size: 20px;
            color: cyan; 
        }
    }

</style>
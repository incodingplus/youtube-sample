<script lang="ts">
    import { browser } from '$app/environment';
    import { onMount } from 'svelte';
    
    // events : onReady, onStateChange, onPlaybackQualityChange, onPlaybackRateChange, onError, onApiChange
    // -1(시작되지 않음)
    // 0(종료됨)
    // 1(재생 중)
    // 2(일시중지됨)
    // 3(버퍼링 중)
    // 5(동영상 신호)

    let arr:string[] = [];
    let curUrl = '';
    let player:YT.Player;
    let ready = false;
    let state = -1;
    let time = 0;
    const init = async () => {
        console.log(location.origin)
        player = new YT.Player('youtube', {
            videoId:'X98pchrBVu0',
            playerVars:{
                origin:location.origin,
                enablejsapi:1,
                controls:0,
            },
            events:{
                onReady(){
                    ready = true
                    curUrl = new URL(player.getVideoUrl()).searchParams.get('v') ?? '';
                },
                onStateChange(e){
                    state = e.data;
                }
            }
        })
        arr = [
            'X98pchrBVu0',
            'o9ha-zXRClo',
            'HOWLElXm040',
            'BOcleeaCL6E',
        ]
    }

    $:if(player && curUrl){
        player.cueVideoById(curUrl);
    }
    onMount(async () => {
        if(browser){
            await init();
            setInterval(() => {
                if('getCurrentTime' in player && 'getDuration' in player){
                    time = player.getCurrentTime() / player.getDuration()
                }
            }, 100);
        } 
    })
</script>
<div>

    <button on:click={() => {
        if(ready && state !== 1 && state !== -1){
            player.playVideo();
        } else {
            console.log(ready, state);
        }
    }}>시작</button>
    <button on:click={() => {
        if(ready && state === 1)
            player.pauseVideo();
    }}>일시정지</button>
    <button on:click={() => {
        if(ready && state === 1)
            player.stopVideo();
    }}>정지</button>
    {(time * 100).toFixed(1)}%<br>
    <select bind:value={curUrl}>
        <option value="" hidden>url을 선택해주세요.</option>
        {#each arr as url}
            <option value={url}>{url}</option>
        {/each}
    </select>
</div>
<div id="youtube"></div>
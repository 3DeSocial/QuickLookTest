<script>
    import { onMount } from 'svelte';
    import * as D3D from '3d-nft-viewer';
    import { USDZExporter } from "three/addons/exporters/USDZExporter.js";

    let nftViewer, image;
    let showUSDZ = false;
    let status = 'Click Image To View in 3D';
    onMount(() => {
        console.log("Component has mounted");

		nftViewer = new D3D.D3DNFTViewer({

			defaultLoader: 'fbx',
			ctrClass: 'card', // Attribute of div containing post and any additional html
			nftDataAttr: 'data-nft', // Attribute of div.ctrClass containing post has hex
			nftsRoute: 'nfts', // Back end route to initialize NFTs
			modelsRoute: 'https://desodata.azureedge.net/unzipped/', // Back end route to load models
			linkText: 'View in 3D',
			linkCtrCls: 'nft-viewer', // container for links such as view in 3d, view in vr
			previewCtrCls: 'post-wrapper', //container element in which to create the preview
			skyboxes: false,
			useShowroom: false,
			scaleModelToHeight: 5,
			scaleModelToWidth: 5,
			scaleModelToDepth: 5, 
			sceneScale: 0.5,  
			playerStartPos: {x:0,y:4,z:10},
			//skyBox: selectedSky,
			lights: [{name:'ambient',intensity : 1},
                    {name:'above',intensity:0.75},
                    {name:'below',intensity:0},
                    {name:'left',intensity:1},
                    {name:'right',intensity:0.5},
                    {name:'front',intensity:5},
                    {name:'back',intensity:0}]
			
		});

       
    });        

    const clickDownload = (event) =>{

        if (event.currentTarget.href === "" || event.currentTarget.href === undefined || event.currentTarget.href === event.currentTarget.baseURI) {
            // The href is empty or is the same as the current URL
            // Stop the click event from doing anything
            event.preventDefault();
        }
    }

    const show3D = () =>{
        status = 'Loading 3D..';
        const url3D = 'https://desodata.azureedge.net/unzipped/e3f5874475ee5b00ff2c9767ba39d1942347aa17fc4c28cdcc4a41929fffa403/gltf/normal/Agent9.glb';
		let params = {
			modelUrl: url3D,
            containerId: 'nft-viewer',
            hideElOnLoad: 'nft-preview-img'
  		}

      		nftViewer.loadModel(params).then(async (item, model, pos)=>{
			
            nftViewer.start3D();
            status = 'Generating QuickLook';

            const exporter = new USDZExporter();
            const arraybuffer = await exporter.parse( nftViewer.scene );
            const blob = new Blob( [ arraybuffer ], { type: 'application/octet-stream' } );
            let link = document.getElementById( 'usdzlink' );
            link.href = URL.createObjectURL( blob );
            status = 'QuickLook Ready: Click Image To View';


                  
        });
    }
</script>

<style>
    h1 {
        color: blue;
    }

    img {

    }

    p{
        text-align: center;
    }

    #nft-viewer, canvas {
        width: 30em;
        margin: 0 auto;
    }
</style>

<svelte:head>
    <title>My Page Title</title>
</svelte:head>

<main>
    <h1>Viewer Test</h1>
    <div class="card">
        <a id="usdzlink" rel="ar" href="" download="asset.usdz" on:click={clickDownload}>
            <div id="nft-viewer" class="post-wrapper">
                <img id="nft-preview-img" class="image  imgReady nft-preview-img" on:click={show3D} src="https://nftz.mypinata.cloud/ipfs/bafybeigfxbuwnrdmsjqudo6vrqutb22b25g63awbr3mwh62tufnpowq2pm" alt="" loading="lazy">
            </div>
        </a>
        <p>{status}</p>
    </div>
</main>
<script lang="ts">
    import { getLang } from "../../../ts/LanguageAdapt";
    import AdaptiveAsset from "../../UIElements/AdaptiveAsset.svelte";
    import Chip from "../../UIElements/ChipElements/Chip.svelte";
    import ChipContainer from "../../UIElements/ChipElements/ChipContainer.svelte";
    import Switch from "../../UIElements/Switch.svelte";
    import ConversionOptions from "../../../ts/TabOptions/ConversionOptions";

    // Initialize rotation settings when this component is loaded
    $: {
        // Set video and audio to copy mode for faster processing
        ConversionOptions.isVideoSelected = true;
        ConversionOptions.isAudioSelected = true;
        ConversionOptions.videoTypeSelected = "copy";
        ConversionOptions.audioTypeSelected = "copy";
        // Enable aspect ratio editing to access rotation
        ConversionOptions.videoOptions.aspectRatio.isBeingEdited = true;
        // Reset width and height to maintain original
        ConversionOptions.videoOptions.aspectRatio.width = -1;
        ConversionOptions.videoOptions.aspectRatio.height = -1;
    }

    let reencodeVideo = false;

    function handleRotationChange(angle: number) {
        ConversionOptions.videoOptions.aspectRatio.rotation = angle;
        // If we need to re-encode for rotation to work properly
        if (reencodeVideo) {
            ConversionOptions.videoTypeSelected = "libx264";
        } else {
            ConversionOptions.videoTypeSelected = "copy";
        }
    }
</script>

<div class="flex hcenter wcenter" style="gap: 10px">
    <AdaptiveAsset asset="rotateleft"></AdaptiveAsset>
    <h2>{getLang("Rotate video:")}</h2>
</div>
<p>
    {getLang(
        "This preset will rotate your video quickly. Select the rotation angle below. Audio will be preserved without re-encoding.",
    )}
</p>
<br />
<p>{getLang("Select rotation angle:")}</p>
<ChipContainer>
    <Chip
        selectionItems={[
            { 
                id: "0.5", 
                display: getLang("Rotate") + " 90°", 
                selected: ConversionOptions.videoOptions.aspectRatio.rotation === 0.5 
            },
            { 
                id: "1", 
                display: getLang("Rotate") + " 180°", 
                selected: ConversionOptions.videoOptions.aspectRatio.rotation === 1 
            },
            { 
                id: "1.5", 
                display: getLang("Rotate") + " 270°", 
                selected: ConversionOptions.videoOptions.aspectRatio.rotation === 1.5 
            },
        ]}
        on:userSelection={({ detail }) => {
            handleRotationChange(parseFloat(detail));
        }}
    ></Chip>
</ChipContainer>
<br />
<Switch
    checked={reencodeVideo}
    on:change={({ detail }) => {
        reencodeVideo = detail;
        handleRotationChange(ConversionOptions.videoOptions.aspectRatio.rotation);
    }}
    text={getLang(
        "Re-encode video (required for some formats, slower but more compatible)",
    )}
></Switch>

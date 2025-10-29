<script lang="ts">
    import { getLang } from "../../../ts/LanguageAdapt";
    import AdaptiveAsset from "../../UIElements/AdaptiveAsset.svelte";
    import Chip from "../../UIElements/ChipElements/Chip.svelte";
    import ChipContainer from "../../UIElements/ChipElements/ChipContainer.svelte";
    import Switch from "../../UIElements/Switch.svelte";
    import ConversionOptions from "../../../ts/TabOptions/ConversionOptions";

    // Rotation angle constants (in multiples of PI for FFmpeg)
    // 90° = 0.5*PI, 180° = 1*PI, 270° = 1.5*PI
    const ROTATION_90 = 0.5;
    const ROTATION_180 = 1;
    const ROTATION_270 = 1.5;

    // Initialize rotation settings when this component is loaded
    ConversionOptions.isVideoSelected = true;
    ConversionOptions.isAudioSelected = true;
    ConversionOptions.audioTypeSelected = "copy";
    ConversionOptions.videoOptions.aspectRatio.isBeingEdited = true;
    ConversionOptions.videoOptions.aspectRatio.width = -1;
    ConversionOptions.videoOptions.aspectRatio.height = -1;

    let reencodeVideo = false;

    function handleRotationChange(angle: number) {
        ConversionOptions.videoOptions.aspectRatio.rotation = angle;
        // Rotation requires re-encoding because FFmpeg filters cannot be used with -vcodec copy
        // When rotation is enabled (angle !== -1), we must re-encode
        if (angle !== -1 || reencodeVideo) {
            ConversionOptions.videoTypeSelected = "libx264";
        } else {
            ConversionOptions.videoTypeSelected = "copy";
        }
    }
    
    // Initialize codec based on current rotation setting
    handleRotationChange(ConversionOptions.videoOptions.aspectRatio.rotation);

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
                id: String(ROTATION_90), 
                display: getLang("Rotate") + " 90°", 
                selected: ConversionOptions.videoOptions.aspectRatio.rotation === ROTATION_90 
            },
            { 
                id: String(ROTATION_180), 
                display: getLang("Rotate") + " 180°", 
                selected: ConversionOptions.videoOptions.aspectRatio.rotation === ROTATION_180 
            },
            { 
                id: String(ROTATION_270), 
                display: getLang("Rotate") + " 270°", 
                selected: ConversionOptions.videoOptions.aspectRatio.rotation === ROTATION_270 
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

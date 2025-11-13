# Asset Usage Detector for Unity 3D

![Settings](Images/Settings.png)

**Available on Asset Store:** https://assetstore.unity.com/packages/tools/utilities/asset-usage-detector-112837

**Forum Thread:** https://forum.unity.com/threads/asset-usage-detector-find-references-to-an-asset-object-open-source.408134/

**Discord:** https://discord.gg/UJJt549AaV

**[GitHub Sponsors ☕](https://github.com/sponsors/yasirkula)**

## ABOUT

This tool helps you find usages of the selected asset(s) and/or scene object(s), i.e. lists the objects that refer to them. It is possible to search for references in the Assets folder (Project view) and/or in the scene(s) of your project. You can also search for references while in Play mode!

## INSTALLATION

- _(via Package Manager)_ click the + button and install the package from the following git URL:
  - `https://github.com/petrknedlik/UnityAssetUsageDetector.git`

## HOW TO

- Open **Window - Asset Usage Detector** window, configure the settings and hit **GO!**
  - or, right click an object and select **Search For References**
- To learn how to interpret the search results and for more instructions, please see the included [README.txt](../Plugins/AssetUsageDetector/README.txt) file
- You can tweak most settings/colors via _Project Settings/petrknedlik/Asset Usage Detector_ page

![SearchResults1](Images/SearchResults1Dark.png)

![SearchResults2](Images/SearchResults2Dark.png)

## SEARCH REFACTORING

While searching for references using the Scripting API, it's possible to get notified of the found references _during_ the search (some references like _Assembly Definition File_ references or _Shader Graph_ references aren't supported) and in most cases, refactor them (e.g. changing all usages of a searched object with something else or _null_). Disabling _Lazy Scene Search_ is recommended while using this feature and it's advised to backup your project beforehand.

To initiate a search using the Scripting API, you need to put your script either in _Editor_ folder or add `AssetUsageDetector.Editor` as reference to your _Assembly Definition File_. Then, you can either create a new instance of `AssetUsageDetectorNamespace.AssetUsageDetector` object and call its `Run` method, or call the `AssetUsageDetectorNamespace.AssetUsageDetectorWindow.ShowAndSearch` method. In either case, you'll be handling the search refactoring in the **searchRefactoring** callback:

```csharp
void ReplaceFontUsages( Font from, Font to )
{
	AssetUsageDetector assetUsageDetector = new AssetUsageDetector();
	assetUsageDetector.Run( new AssetUsageDetector.Parameters()
	{
		objectsToSearch = new Object[] { from },
		lazySceneSearch = false,
		searchRefactoring = ( searchMatch ) =>
		{
			Debug.LogFormat( "Found a {0} reference from {1} to {2}", searchMatch.GetType().Name, searchMatch.Source, searchMatch.Value );
			searchMatch.ChangeValue( to );
		}
	} );
}
```

**NOTE:** Refactored references won't be reflected to the returned search results, old references will continue to be displayed. After saving the changes (modified scenes) and initiating another search, correct search results will be shown.

**NOTE2:** After refactoring the references, consider performing a normal search to see if all references were correctly refactored. If some references weren't refactored (even though they could've been), feel free to report it.

## KNOWN LIMITATIONS

- _static_ variables aren't searched
- _Resources.Load_ usages can't be found
- *ref struct*s can't be searched
- _GUIText_ materials aren't searched
- Textures in _Lens Flares_ can't be searched

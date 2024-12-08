`float4 SceneTextureLookup(float2 UV, int SceneTextureIndex, bool Filtered)`

- UV: 即UV坐标，像素的位置。例如：UV是 (0.5, 0.5) 意味着这个像素位于图像的正中央。

- SceneTextureIndex: 决定从哪个缓存纹理中抽样。下表就是缓存纹理和其Index值的对应关系。例如：我们想从Post Process Input 0中抽样，那么就要将14作为index值。

- Filtered: 被抽样纹理是否需要双线性过滤（bilinear filtering），这个值一般会被设为false。
 ![[Pasted image 20240514121519.png]]
	注：在4.19版本以前，我们的添加**TextureCoordinate**节点作为输入才能够获得UV。在4.19以后正确的方法是使用`GetDefaultSceneTextureUV()`并提供你想要的index.

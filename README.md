# Vision Skills

本仓库实现了视觉相关Agent Skills
- [image-generation](./skills/image-generation): 基于 Gemini (Nano Banana)的文生图、图像编辑、多图合成
- [video-generation](./skills/video-generation): 基于 Gemini Veo 的图生视频/文生视频

**配置环境变量**
由于 Gemini 相关技能需要 API Key，请在使用前配置环境变量：
```bash
export GEMINI_API_KEY="YOUR_KEY"
```

## Examples
<table>
  <tr>
    <th></th>
    <th>skill运行过程</th>
    <th>结果</th>
  </tr>
  <tr>
    <td>图像生成</td>
    <td><img src="assets/image-generation-codex.png" alt="image-generation-codex" width="320"></td>
    <td><img src="assets/kitten_1.png" alt="kitten_1" width="320"></td>
  </tr>
  <tr>
    <td>图像编辑</td>
    <td><img src="assets/image-edit-codex.png" alt="image-edit-codex" width="320"></td>
    <td><img src="assets/kitten_cartoon_1.png" alt="kitten_cartoon_1" width="320"></td>
  </tr>
  <tr>
    <td>视频生成</td>
    <td><img src="assets/image-to-video-codex.png" alt="image-to-video-codex" width="320"></td>
    <td><img src="assets/kitten_cartoon_1_fun_0.gif" alt="kitten_cartoon_1_fun_0" width="320"></td>
  </tr>
</table>

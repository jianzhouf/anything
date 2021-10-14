## 基于remaxjs实现的支付宝小程序海报


```tsx
// index.ali.tsx
import React, {
  forwardRef,
  useEffect,
  useImperativeHandle,
  useState,
} from "react";
import { Canvas } from "@remax/ali";
import { showToast } from "@/api";

export interface CreatePosterProps {
  imgUrl: string;
  title: string;
}

export interface TempFilePathRes {
  apFilePath: string;
  filePath: string;
}

export interface CreatePosterMethods {
  save(callback?: () => void): void;
  getTempFilePath(): Promise<TempFilePathRes>;
}

const CreatePoster = forwardRef<CreatePosterMethods, CreatePosterProps>(
  ({ imgUrl, title }, ref) => {
    const [ctx, setCtx] = useState<my.ICanvasContext>();

    // 优化展示效果，画完后展示
    const [opacity, setOpacity] = useState(0);

    const roundRect = (
      x: number,
      y: number,
      w: number,
      h: number,
      r: number[]
    ) => {
      var min_size = Math.min(w, h);

      function formatradius(_r: number) {
        if (_r > min_size / 2) {
          return min_size / 2;
        } else {
          return _r;
        }
      }
      // 开始绘制
      ctx?.beginPath();
      ctx?.moveTo(x + r[0], y);

      // right-top
      (ctx as any)?.arcTo(x + w, y, x + w, y + h, formatradius(r[1]));
      // right-bottom
      (ctx as any)?.arcTo(x + w, y + h, x, y + h, formatradius(r[2]));
      //left-bottom
      (ctx as any)?.arcTo(x, y + h, x, y, formatradius(r[3]));
      // left-top
      (ctx as any)?.arcTo(x, y, x + w, y, formatradius(r[0]));
      ctx?.closePath();
    };

    const getImageDataUrl = () => {
      if (!ctx) {
        throw new Error("缺少ctx");
      }
      return new Promise((resolve) => {
        ctx.drawImage(imgUrl, 0, 0, 432, 432);

        ctx.draw(false, () => {
          // 改变商品主图尺寸
          (ctx as any)
            .toDataURL({
              x: 0,
              y: 0,
              width: 432,
              height: 432,
            })
            .then(resolve);
        });
      });
    };

    const createText = (text: string) => {
      if (!ctx) {
        throw new Error("缺少ctx");
      }
      const arr = text.split("\n");
      switch (arr.length) {
        case 1:
          const textWidth = ctx.measureText(arr[0]).width;
          ctx.fillText(arr[0], (432 - textWidth) / 2, 519);
          break;
        case 2:
          ctx.fillText(arr[0], 28, 503);
          ctx.fillText(arr[1], 28, 535);
          break;
      }
    };

    useEffect(() => {
      if (!ctx || !title || !imgUrl) {
        return;
      }
      setOpacity(0);
      getImageDataUrl().then((dataURL) => {
        // 白色背景
        ctx.setFillStyle("white");
        roundRect(0, 0, 432, 625, [12, 12, 12, 12]);
        ctx.fill();

        // 商品主图
        const parttern = (ctx as any).createPattern(dataURL, "no-repeat");
        ctx.setFillStyle(parttern);
        roundRect(0, 0, 432, 432, [12, 12, 0, 0]);
        ctx.fill();

        // 模糊
        ctx.drawImage(
          "模糊图片url",
          0,
          360,
          432,
          134
        );

        // 文案背景
        const grd = ctx.createLinearGradient(0, 0, 0, 158);
        grd.addColorStop(0, "#FFD4CD");
        grd.addColorStop(1, "#FFF1F1");
        ctx.setFillStyle(grd as any);
        roundRect(12, 430, 408, 158, [30, 30, 30, 30]);
        ctx.fill();
        // 文案
        ctx.setFontSize(24);
        ctx.setFillStyle("#A5675D");
        createText(title);

        // 我中奖了
        ctx.drawImage(
          "iconUrl",
          70,
          358,
          272,
          121
        );

        ctx.draw(false, () => {
          setOpacity(1);
        });
      });
    }, [ctx, title, imgUrl]);

    useEffect(() => {
      setCtx(my.createCanvasContext("createPoster"));
    }, []);

    const getTempFilePath = () => {
      return new Promise<TempFilePathRes>((r) => {
        if (ctx) {
          ctx.toTempFilePath({
            success: r,
          } as any);
        } else {
          throw new Error("缺少ctx");
        }
      });
    };

    const handleSave = (callback = () => {}) => {
      getTempFilePath().then((res) => {
        (my as any).saveImageToPhotosAlbum({
          filePath: res.apFilePath,
          success() {
            showToast({
              content: "保存成功",
            });
            callback();
          },
          fail(e: any) {
            showToast({
              content: e.errorMessage,
            });
          },
        });
      });
    };

    useImperativeHandle(ref, () => ({
      save: handleSave,
      getTempFilePath,
    }));

    return (
      <Canvas
        width="432"
        height="625"
        style={{ width: 432, height: 625, opacity: opacity }}
        id="createPoster"
        onTap={handleSave}
      ></Canvas>
    );
  }
);

export default CreatePoster;

```

```tsx
//index.tsx
import React from "react";

export interface CreatePosterProps {
  imgUrl: string;
  title: string;
}
export interface TempFilePathRes {
  apFilePath: string;
  filePath: string;
}
export interface CreatePosterMethods {
  save(callback?: () => void): void;
  getTempFilePath(): Promise<TempFilePathRes>;
}
declare const CreatePoster: React.ForwardRefExoticComponent<
  React.PropsWithoutRef<CreatePosterProps> &
    React.RefAttributes<CreatePosterMethods>
>;

export default CreatePoster;
```

---
title: 自定义View -- 动画
date: 2016-12-26 09:23:14
tags: [View, 自定义View, Animation]
categories: Android

---

# 利用 属性动画




# 利用 ValueAnimation 实现动画


在自定义View 中添加下面的代码：

```java
    private ValueAnimator mValueAnimator;
    private int mProgress;
    private long mAnimationTime;
    private void initAnimate() {
        mValueAnimator = ValueAnimator.ofInt(0, 100);
        mValueAnimator.setDuration(2000);
//        mValueAnimator.setRepeatCount(ValueAnimator.RESTART);
        mValueAnimator.setInterpolator(new LinearInterpolator());

        mValueAnimator.addUpdateListener(animation -> {
            mProgress = (int) animation.getAnimatedValue();
            Log.d(TAG, "run: mProgress" + mProgress);
            invalidate();
        });

        mValueAnimator.addListener(new Animator.AnimatorListener() {
            @Override
            public void onAnimationStart(Animator animation) {

            }

            @Override
            public void onAnimationEnd(Animator animation) {
                if (((int) mValueAnimator.getAnimatedValue()) >= 98) {
                    mAnimationTime = 0;
                }
            }

            @Override
            public void onAnimationCancel(Animator animation) {

            }

            @Override
            public void onAnimationRepeat(Animator animation) {

            }
        });
    }

    public void stop() {
        if (mValueAnimator != null) {
            mAnimationTime = mValueAnimator.getCurrentPlayTime();
            mValueAnimator.cancel();  // 保持停止状态
        }
    }

    public void start() {
        if (mValueAnimator != null) {
            mValueAnimator.start();
            mValueAnimator.setCurrentPlayTime(mAnimationTime);
        }
    }

    public void repeatStart() {
        repeatStart(ValueAnimator.REVERSE);
    }

    public void repeatStart(int repeatMode) {
        if (repeatMode == ValueAnimator.RESTART || repeatMode == ValueAnimator.REVERSE) {
            mValueAnimator.setRepeatMode(repeatMode);
        } else {
            Log.e(TAG, "repeatStart: repeatMode error");
            mValueAnimator.setRepeatMode(ValueAnimator.RESTART);
        }
        mValueAnimator.setRepeatCount(ValueAnimator.INFINITE);
        start();
    }

    @Override
    protected void onDetachedFromWindow() {
        super.onDetachedFromWindow();
        stop();
    }

    @Override
    protected void onAttachedToWindow() {
        super.onAttachedToWindow();
//        run();
    }
```

`initAnimate()` 需要在 View 初始化中调用。


## 分步动画例子

```java
    // 绘制方法
    private void drawCenter(Canvas canvas) {
        if (mStep == 0 || mStep == 1) {
            canvas.drawCircle(mWidth / 2, mHeight / 2, mWidth / 4, mCenterPaint);
        } else if (mStep == 2) {
            int radius = mProgress * (mWidth / 3 - mWidth / 4) / 100 + mWidth / 4;
            canvas.drawCircle(mWidth / 2, mHeight / 2, radius, mCenterPaint);
        }
    }

    private void circleLoading(Canvas canvas) {
        if (mStep == 0 || mStep == 1) {
            int startAngle = mProgress * 720 / 100;
            RectF rectF = new RectF(0 + 10, 0 + 10, mWidth - 10, mHeight - 10);
            canvas.drawArc(rectF, startAngle, 80, false, mCirclePaint);
            canvas.drawArc(rectF, startAngle + 80 + 40, 80, false, mCirclePaint); // 120 - 200
            canvas.drawArc(rectF, startAngle + 80 + 40 + 80 + 40, 80, false, mCirclePaint);
        } else if (mStep == 2) {
            // 矩形框：（10，10）（mWidth-10，mHeight-10）--》 （30，30）（mWidth-30，mHeight-30）
            int differ = mProgress * 20 / 100;
            // 圆弧： 80 --》 120
            int angleDiffer = mProgress * 40 / 100;
            int startAngle = mProgress * 360 / 100;
            RectF rectF = new RectF(0 + 10 + differ, 0 + 10 + differ, mWidth - 10 - differ, mHeight - 10 - differ);
            canvas.drawArc(rectF, startAngle, 80 + angleDiffer, false, mCirclePaint);
            canvas.drawArc(rectF, startAngle + 80 + 40, 80 + angleDiffer, false, mCirclePaint); // 120 - 200
            canvas.drawArc(rectF, startAngle + 80 + 40 + 80 + 40, 80 + angleDiffer, false, mCirclePaint);
        }
    }



    // ------------ 分步动画

    /**
     * 动画阶段
     */
    private int mStep;

    public void end() {
        // progress 0 -> 100 (100=0 的状态) 收尾动画
        ValueAnimator valueAnimator1 = ValueAnimator.ofInt(0, 100);
        valueAnimator1.setDuration(300);
        valueAnimator1.setInterpolator(new LinearInterpolator());
        valueAnimator1.addUpdateListener(animation -> {
            mStep = 2;
            mProgress = ((int) animation.getAnimatedValue());
            invalidate();
        });


        // progress 收尾 ( 如果收尾调用的时候 mProgress 不在 100 的位置)
        ValueAnimator valueAnimator = ValueAnimator.ofInt(mProgress, 100);
        valueAnimator.setDuration(100);
        valueAnimator.setInterpolator(new LinearInterpolator());
        valueAnimator.addUpdateListener(animation -> {
            mStep = 1;
            mProgress = ((int) animation.getAnimatedValue());
            invalidate();
        });
        valueAnimator.addListener(new Animator.AnimatorListener() {
            @Override
            public void onAnimationStart(Animator animation) {
                stopStepOneAnimator();
            }

            @Override
            public void onAnimationEnd(Animator animation) {
                valueAnimator1.start();
            }

            @Override
            public void onAnimationCancel(Animator animation) {

            }

            @Override
            public void onAnimationRepeat(Animator animation) {

            }
        });
        valueAnimator.start();
    }

    private void stopStepOneAnimator() {
        if (mStepOneAnimator != null && mStepOneAnimator.isStarted()) {
            mStepOneAnimator.cancel();
        }
    }
```

## 分状态动画

```java
    // draw
    private void drawCenter(Canvas canvas) {
        mCenterPaint.setColor(mIDLEColor);
        if (mStatus == LoadingViewStatus.IDLE) {
            canvas.drawCircle(mWidth / 2, mHeight / 2, mWidth / 6, mCenterPaint);
        } else if (mStatus == LoadingViewStatus.SELECTED) {
            canvas.drawCircle(mWidth / 2, mHeight / 2, mWidth / 6, mCenterPaint);
        } else if (mStatus == LoadingViewStatus.LOADING) {
            canvas.drawCircle(mWidth / 2, mHeight / 2, mWidth / 6, mCenterPaint);
        } else if (mStatus == LoadingViewStatus.OK) {
            mCenterPaint.setColor(mOkColor);
            canvas.drawCircle(mWidth / 2, mHeight / 2, mWidth / 6, mCenterPaint);
        }
    }

    private void circleLoading(Canvas canvas) {
        switch (mStatus) {
            case LoadingViewStatus.IDLE:
                break;
            case LoadingViewStatus.LOADING:
                int angleDiffer = mProgress * 180 / 100;
                RectF rectF = new RectF(10, 10, mWidth - 10, mHeight - 10);
                canvas.drawArc(rectF, 0 + angleDiffer, 80, false, mCirclePaint);
                canvas.drawArc(rectF, 80 + 40 + angleDiffer, 80, false, mCirclePaint);
                canvas.drawArc(rectF, 80 + 40 + 80 + 40 + angleDiffer, 80, false, mCirclePaint);
                break;
            case LoadingViewStatus.SELECTED:
                int radius = mProgress * ((mWidth - 10) / 2) / 100;
                canvas.drawCircle(mWidth / 2, mHeight / 2, radius, mCirclePaint);
                break;
            case LoadingViewStatus.OK:
                int radius2 = mProgress * ((mWidth - 10) / 2) / 100;
                canvas.drawCircle(mWidth / 2, mHeight / 2, radius2, mCirclePaint);
                break;
        }
    }


    // loading view mStatus --------------------------------
    // color
    private int mOkColor;
    private int mIDLEColor;
    private int mSelectedColor;
    private ValueAnimator mToIDLEAniamte;
    private ValueAnimator mLoadingAnimate;
    private boolean mAnimating;
    private ValueAnimator mToSelectedAnimate;
    private ValueAnimator mToOkAnimate;

    private int mProgress;
    private int mStatus = 0; // 0:idle , 1:loading , 2 selected

    public void setStatus(int status) {
        int lastStatus = mStatus;
        mStatus = status;
        stopAllAnimate();

        switch (mStatus) {
            case LoadingViewStatus.IDLE:
                startToIDLEAnimate();
                break;
            case LoadingViewStatus.LOADING:
                startLoadingAnimate();
                break;
            case LoadingViewStatus.SELECTED:
                startToSelectedAnimate();
                break;
            case LoadingViewStatus.OK:
                startToOkAnimate();
                break;
        }
    }

    private void startToOkAnimate() {
        if (mToOkAnimate == null) {
            mToOkAnimate = ValueAnimator.ofInt(100, 0);
            mToOkAnimate.setDuration(200);
            mToOkAnimate.addUpdateListener(animation -> {
                mProgress = ((int) animation.getAnimatedValue());
                invalidate();
            });
            mToOkAnimate.addListener(listener);
        }
        mProgress = 100;
        mToOkAnimate.start();
    }

    private void startToSelectedAnimate() {
        if (mToSelectedAnimate == null) {
            mToSelectedAnimate = ValueAnimator.ofInt(0, 100);
            mToSelectedAnimate.setDuration(200);
            mToSelectedAnimate.addUpdateListener(animation -> {
                mProgress = ((int) animation.getAnimatedValue());
                invalidate();
            });
            mToSelectedAnimate.addListener(listener);
        }
        mProgress = 0;
        mToSelectedAnimate.start();
    }

    private void startLoadingAnimate() {
        if (mLoadingAnimate == null) {
            mLoadingAnimate = ValueAnimator.ofInt(0, 100);
            mLoadingAnimate.setRepeatMode(ValueAnimator.RESTART);
            mLoadingAnimate.setRepeatCount(ValueAnimator.INFINITE);
            mLoadingAnimate.addUpdateListener(animation -> {
                mProgress = ((int) animation.getAnimatedValue());
                invalidate();
            });
            mLoadingAnimate.addListener(listener);
        }
        mProgress = 0;
        mLoadingAnimate.start();
    }

    private void startToIDLEAnimate() {
        if (mToIDLEAniamte == null) {
            mToIDLEAniamte = ValueAnimator.ofInt(0, 100);
            mToIDLEAniamte.addUpdateListener(animation -> {
                mProgress = ((int) animation.getAnimatedValue());
                invalidate();
            });
            mToIDLEAniamte.addListener(listener);
        }
        mProgress = 0;
        mToIDLEAniamte.start();
    }


    private void stopAllAnimate() {
        if (mLoadingAnimate != null && mLoadingAnimate.isStarted()) {
            mLoadingAnimate.end();
        }
        if (mToIDLEAniamte != null && mToIDLEAniamte.isStarted()) {
            mToIDLEAniamte.end();
        }
        if (mToSelectedAnimate != null && mToSelectedAnimate.isStarted()) {
            mToSelectedAnimate.end();
        }
        if (mToOkAnimate != null && mToOkAnimate.isStarted()) {
            mToOkAnimate.end();
        }

    }


    Animator.AnimatorListener listener = new Animator.AnimatorListener() {
        @Override
        public void onAnimationStart(Animator animation) {
            mAnimating = true;
        }

        @Override
        public void onAnimationEnd(Animator animation) {
            mAnimating = false;
        }

        @Override
        public void onAnimationCancel(Animator animation) {

        }

        @Override
        public void onAnimationRepeat(Animator animation) {

        }
    };

    public static class LoadingViewStatus {
        public static final int IDLE = 0;
        public static final int LOADING = 1;
        public static final int SELECTED = 2;
        public static final int OK = 3;
    }

```

效果：

![https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/circleview.gif](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/circleview.gif)



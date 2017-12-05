---
title: View 移动最佳实践
date: 2017-12-01 09:13:12
tags: 触摸事件
category: Android

---




```java
        mTouchView.setOnTouchListener(new View.OnTouchListener() {

            private float mStartY;
            private float mStartX;

            private float mDownViewX;
            private float mDownViewY;

            @Override
            public boolean onTouch(View v, MotionEvent event) {

                if (mIsAnimating) {
                    return false;
                }

//                mDetector.onTouchEvent(event);

                int action = event.getAction();
                switch (action) {
                    case MotionEvent.ACTION_DOWN:
                        mDownViewX = mTouchView.getX();
                        mDownViewY = mTouchView.getY();

                        mStartX = event.getRawX();
                        mStartY = event.getRawY();
                        Log.d(TAG, "onTouch: ACTION_DOWN   getRawX=" + mStartX + ",getRawY=" + mStartY);
                        Log.d(TAG, "onTouch: ACTION_DOWN   x=" + mDownViewX + ",y=" + mDownViewY);
                        break;
                    case MotionEvent.ACTION_MOVE:

                        float newX = event.getRawX();
                        float newY = event.getRawY();
                        float offsetX = newX - mStartX;
                        float offsetY = newY - mStartY;

                        mTouchView.setX(mTouchView.getX() + offsetX);
                        mTouchView.setY(mTouchView.getY() + offsetY);

                        Log.d(TAG, "onTouch: ACTION_MOVE   offsetX=" + offsetX +
                                ",offsetY=" + offsetY);

                        mStartX = newX;
                        mStartY = newY;

                        break;
                    case MotionEvent.ACTION_UP:

                        //
                        autoScrollTo(mDownViewX, mDownViewY);

                        break;
                    case MotionEvent.ACTION_CANCEL:

                        break;
                }
                return true;
            }
        });
```



```java
    private boolean mIsAnimating;
    private void autoScrollTo(float x, float y) {
        Log.d(TAG, "autoScrollTo: x=" + x + ", y=" + y + ",, mTouchView.getX()" + mTouchView.getX() +
                ",,mTouchView.getY()" + mTouchView.getY());
        ViewPropertyAnimator animate = mTouchView.animate();
        animate.translationXBy(x - mTouchView.getX());
        animate.translationYBy(y - mTouchView.getY());
        animate.setListener(new Animator.AnimatorListener() {
            @Override
            public void onAnimationStart(Animator animation) {
                mIsAnimating = true;
            }

            @Override
            public void onAnimationEnd(Animator animation) {
                mIsAnimating = false;
                Log.d(TAG, "onAnimationEnd: x=" + mTouchView.getX() + ", y=" + mTouchView.getY());
            }

            @Override
            public void onAnimationCancel(Animator animation) {

            }

            @Override
            public void onAnimationRepeat(Animator animation) {

            }
        });
        animate.setDuration(800);
        animate.start();

//        mTouchView.setX(x);
//        mTouchView.setY(y);
    }
```
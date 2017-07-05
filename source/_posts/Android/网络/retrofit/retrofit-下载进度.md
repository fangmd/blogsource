---
title: retrofit - 下载进度实现
date: 2016-11-16 12:18:12
tags: [Retrofit]
category: android

---

# 定义一个 Interceptor

public class DownloadProgressInterceptor implements Interceptor {
    private DownloadProgressListener progressListener;

    public DownloadProgressInterceptor(DownloadProgressListener progressListener) {
        this.progressListener = progressListener;
    }

    @Override
    public Response intercept(Chain chain) throws IOException {
        Response originalResponse = chain.proceed(chain.request());
        return originalResponse.newBuilder()
                .body(new DownloadProgressResponseBody(originalResponse.body(), progressListener))
                .build();
    }

    private static class DownloadProgressResponseBody extends ResponseBody {

        private final ResponseBody responseBody;
        private final DownloadProgressListener progressListener;
        private BufferedSource bufferedSource;

        public DownloadProgressResponseBody(ResponseBody responseBody,
                                            DownloadProgressListener progressListener) {
            this.responseBody = responseBody;
            this.progressListener = progressListener;
        }

        @Override
        public MediaType contentType() {
            return responseBody.contentType();
        }

        @Override
        public long contentLength() {
            return responseBody.contentLength();
        }

        @Override
        public BufferedSource source() {
            if (bufferedSource == null) {
                bufferedSource = Okio.buffer(source(responseBody.source()));
            }
            return bufferedSource;
        }

        private Source source(Source source) {
            return new ForwardingSource(source) {
                long totalBytesRead = 0L;

                @Override
                public long read(Buffer sink, long byteCount) throws IOException {
                    long bytesRead = super.read(sink, byteCount);
                    // read() returns the number of bytes read, or -1 if this source is exhausted.
                    totalBytesRead += bytesRead != -1 ? bytesRead : 0;

                    if (null != progressListener) {
                        progressListener.update(totalBytesRead, responseBody.contentLength(), bytesRead == -1);
                    }
                    return bytesRead;
                }
            };
        }
    }

    public interface DownloadProgressListener {
        void update(long bytesRead, long contentLength, boolean done);
    }
}

# 使用

    OkHttpClient client = new OkHttpClient.Builder()
            .addInterceptor(new DownloadProgressInterceptor(new DownloadProgressInterceptor.DownloadProgressListener() {

                @Override
                public void update(long bytesRead, long contentLength, boolean done) {

                }
            })).build();


    mDownService = new Retrofit.Builder()
            .client(client)
            .baseUrl(Api.getInstance().getServiceAddress())
            .build()
            .create(DownService.class);

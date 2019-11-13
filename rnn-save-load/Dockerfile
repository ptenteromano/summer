FROM python:3.6
ADD . /root/
WORKDIR /root/
RUN pip install --upgrade pip && \
    pip install --no-cache-dir tensorflow && \
    mkdir rnn
CMD ["python", "rnn_saveload.py"]
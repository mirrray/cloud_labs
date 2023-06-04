import pandas as pd
import numpy as np
from smart_open import smart_open
from matplotlib import pylab as plt
import os
import boto3
from botocore.exceptions import ClientError
import logging

def get_dict(link):
    array = []
    for line in smart_open(link, 'rb'):
        array.append(line.decode('utf8'))
    array = array[1:]
    for i in range(len(array)):
        tmp = array[i].replace("\n", "").split(",")
        array[i] = {'date': tmp[-1], 'exchange_rate': float(tmp[-3])}
    return array


def make_graff(array, name):
    df = pd.DataFrame(array)
    x = df['date']
    y = df['exchange_rate']
    plt.figure(figsize=(15, 10))
    plt.xticks(rotation=90)
    plt.ylabel('Exchange Rates', fontsize=12)
    plt.xticks(np.arange(0, len(array), 10))
    plt.plot(x, y)
    plt.savefig(name)


def upload_file(file_name, bucket, object_name=None):
    """Upload a file to an S3 bucket

    :param file_name: File to upload
    :param bucket: Bucket to upload to
    :param object_name: S3 object name. If not specified then file_name is used
    :return: True if file was uploaded, else False
    """

    # If S3 object_name was not specified, use file_name
    if object_name is None:
        object_name = os.path.basename(file_name)
    s3_client = boto3.client('s3')
    try:
        response = s3_client.upload_file(file_name, bucket, object_name)
    except ClientError as e:
        logging.error(e)
        return False
    return True


euro = get_dict('s3://chenskiy-bucket/eur.csv')
make_graff(euro, "eur1.png")
upload_file("eur1.png", 'chenskiy-bucket', object_name=None)
print("Success")

FROM amazon/aws-lambda-python

ARG AWS_ACCESS_KEY_ID
ARG AWS_SECRET_ACCESS_KEY
WORKDIR ${LAMBDA_TASK_ROOT}

ENV AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID \
    AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY

RUN yum install git -y && yum -y install gcc-c++
COPY requirements_inference.txt .
RUN pip install -r requirements_inference.txt --target "${LAMBDA_TASK_ROOT}"
COPY  lambda_handler.py ${LAMBDA_TASK_ROOT}
COPY . .

ENV LC_ALL=C.UTF-8
ENV LANG=C.UTF-8
RUN pip install "dvc[s3]"

# configuring remote server in dvc
RUN dvc init --no-scm
RUN dvc remote add -d model-store s3://mlopsyogi/mlops/

# pulling the trained model
RUN dvc pull dvcfiles/trained_model.dvc
RUN python lambda_handler.py

CMD [ "lambda_handler.lambda_handler"]
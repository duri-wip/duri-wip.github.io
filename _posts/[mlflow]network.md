mlflow 서버를 띄우면서 네트워크를 묶어서 학습 이미지를 띄우지 않으면 .. default artifact storage 가 연결되지 않는 문제가 있다..

트러블 슈팅
docker network를 지정해서 묶어주고 이를 github actions에서 자동화 파이프라인으로 구성하는 과정에서 네트워크설정이 잘 안되고 디버깅이어려운 문제가 있음
git pull 하는 과정에서 있는 문제라고 생각..

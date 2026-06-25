# Bao cao Day 21 - CI/CD cho AI Systems

## 1. Ket qua MLflow va bo sieu tham so da chon

Trong Buoc 1, em da chay nhieu lan huan luyen voi cac bo sieu tham so khac nhau va so sanh ket qua tren MLflow UI. Bo sieu tham so tot nhat duoc chon cho mo hinh RandomForestClassifier la:

```yaml
n_estimators: 800
max_depth: null
min_samples_split: 2
min_samples_leaf: 1
max_features: sqrt
n_jobs: -1
```

Ly do chon: bo tham so nay cho ket qua cao nhat trong cac lan thu nghiem local, voi accuracy ban dau dat khoang 0.6840 va f1_score dat khoang 0.6827 tren tap eval.

## 2. Ket qua CI/CD va Eval Gate

Pipeline GitHub Actions gom 4 job:

| Job | Vai tro |
|---|---|
| Unit Test | Kiem tra code huan luyen voi du lieu gia lap |
| Train | Pull data bang DVC, huan luyen model, upload model len Cloud Storage |
| Eval | Kiem tra accuracy co vuot nguong 0.70 hay khong |
| Deploy | SSH vao VM va restart service FastAPI |

O lan chay voi du lieu ban dau, Eval gate da chan deploy vi accuracy = 0.6840 nho hon nguong 0.70. Day la hanh vi dung cua pipeline: model chua dat chat luong thi khong duoc trien khai.

## 3. Ket qua them du lieu moi o Buoc 3

Sau khi bo sung du lieu moi tu `train_phase2.csv` vao tap huan luyen va cap nhat DVC, mot commit du lieu moi da kich hoat pipeline tu dong. Ket qua so sanh:

| Chi so | Buoc 2 (truoc khi them du lieu) | Buoc 3 (sau khi them du lieu) |
|---|---:|---:|
| accuracy | 0.6840 | 0.7440 |
| f1_score | 0.6827 | 0.7433 |

Sau khi them du lieu, model moi dat accuracy = 0.7440 va f1_score = 0.7433. Accuracy da vuot nguong 0.70, nen Eval gate cho phep Deploy job chay. Pipeline hoan thanh ca 4 job mau xanh va model moi duoc trien khai len VM thanh cong.

## 4. Kiem tra API sau deploy

API FastAPI tren VM da duoc kiem tra bang cac endpoint:

```text
GET /health
```

Ket qua:

```json
{"status":"ok"}
```

Endpoint du doan:

```text
POST /predict
```

Ket qua voi mau test:

```text
prediction = 0
label = thap
```

## 5. Kho khan va cach xu ly

Mot so van de gap phai trong qua trinh thuc hien:

| Van de | Cach xu ly |
|---|---|
| GitHub Actions bi tat tren fork | Enable Actions trong tab Actions cua GitHub |
| Eval gate chan deploy vi accuracy < 0.70 | Ghi nhan day la hanh vi dung, sau do them du lieu moi o Buoc 3 de cai thien accuracy |
| Deploy loi do `VM_SSH_KEY` khong dung dinh dang | Tao lai SSH key, them public key vao VM va dan dung private key vao GitHub Secret |
| PowerShell khong co `curl.exe` | Dung `Invoke-WebRequest` va `Invoke-RestMethod` de test API |

## 6. Ket luan

Lab da xay dung duoc mot pipeline MLOps hoan chinh: du lieu duoc quan ly bang DVC tren Cloud Storage, model duoc huan luyen va danh gia tu dong bang GitHub Actions, Eval gate chan cac model khong dat nguong chat luong, va model dat yeu cau duoc deploy len VM duoi dang REST API bang FastAPI.

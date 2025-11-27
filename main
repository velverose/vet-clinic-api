from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from typing import List, Optional

app = FastAPI(
    title="Vet Clinic API",
    description="Микросервис для хранения и обновления информации о собаках.",
    version="1.0.0"
)

# ---------------------------
#   МОДЕЛИ
# ---------------------------

class DogBase(BaseModel):
    name: str
    age: int
    breed: str
    vaccinated: bool


class DogCreate(DogBase):
    pass


class DogUpdate(BaseModel):
    name: Optional[str] = None
    age: Optional[int] = None
    breed: Optional[str] = None
    vaccinated: Optional[bool] = None


class Dog(DogBase):
    id: int


# ---------------------------
#   "БД" — ХРАНЕНИЕ В ПАМЯТИ
# ---------------------------

dogs: List[Dog] = []
current_id = 1


# ---------------------------
#   ЭНДПОИНТЫ
# ---------------------------

@app.get("/dogs", response_model=List[Dog])
def get_dogs():
    return dogs


@app.post("/dogs", response_model=Dog, status_code=201)
def create_dog(dog: DogCreate):
    global current_id

    new_dog = Dog(id=current_id, **dog.dict())
    dogs.append(new_dog)
    current_id += 1

    return new_dog


@app.get("/dogs/{dog_id}", response_model=Dog)
def get_dog(dog_id: int):
    for dog in dogs:
        if dog.id == dog_id:
            return dog
    raise HTTPException(status_code=404, detail="Dog not found")


@app.put("/dogs/{dog_id}", response_model=Dog)
def update_dog(dog_id: int, data: DogUpdate):
    for dog in dogs:
        if dog.id == dog_id:
            updated = dog.dict()
            for key, value in data.dict(exclude_unset=True).items():
                updated[key] = value

            dog_index = dogs.index(dog)
            dogs[dog_index] = Dog(**updated)
            return dogs[dog_index]

    raise HTTPException(status_code=404, detail="Dog not found")


@app.delete("/dogs/{dog_id}", status_code=204)
def delete_dog(dog_id: int):
    for dog in dogs:
        if dog.id == dog_id:
            dogs.remove(dog)
            return
    raise HTTPException(status_code=404, detail="Dog not found")

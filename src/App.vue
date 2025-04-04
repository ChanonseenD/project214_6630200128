
  <!-- ช่องทำตรงแก้ไขข้อมูล -->
  <template>
    <div class="container-fluid main-bg">
      <div class="row justify-content-center align-items-center vh-100">
        <!-- Profile Section -->
         <div class="leftdetail">
          <div class="col-md-4 text-center ildetail">
            <div class="front">
              <img class="card-img-top profile-img mx-auto d-block" src="./assets/img2.jpg" alt="Profile">
            </div>
            <div class="back">
              <img class="card-img-top profile-img mx-auto d-block" src="./assets/pn1.jpg" alt="Profile">
            </div> 
        </div>
          <div class="card-body">
                <h5 class="card-title">{{ dataUser.name }}</h5>
                <p class="card-text text-muted">นักศีกษา | มหาลัย</p>
                <button class="btn btn-primary mt-2" @click="openEditUserModal">แก้ไขข้อมูล</button>
              </div>
         </div>


        <!-- Menu Section -->
        <div class="col-md-6">
          <div class="menu-container text-center">
            <h2>ดีครับ,ผม<br /><strong>{{ dataUser.name }}</strong></h2>
            <p class="text-muted">Dek Comsci tee che Not :D </p>
            <p><strong>รหัสนิสิต:</strong> {{ dataUser.id }}</p>
            <p><strong>ชื่อ:</strong> {{ dataUser.name }}</p>
            <p><strong>รหัสสาขา:</strong> {{ dataUser.major_id }}</p>
            <p><strong>สาขา:</strong> {{ dataUser.major_name }}</p>
            <p><strong>โรงเรียนเดิม:</strong> {{ dataUser.old_school }}</p>

            <button class="btn btn-success mt-3" @click="openAddSubjectModal">+ รายวิชา</button>

            <!-- Subjects Table -->
            <table class="table mt-3">
              <thead>
                <tr>
                  <th>#</th>
                  <th>รหัสวิชา</th>
                  <th>ชื่อวิชา</th>
                  <th>เกรด</th>
                  <th>หน่วยกิต</th>
                  <th>จัดการ</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(subject, index) in subjects" :key="index">
                  <td>{{ index + 1 }}</td>
                  <td>{{ subject.id }}</td>
                  <td>{{ subject.name }}</td>
                  <td>{{ subject.grade }}</td>
                  <td>{{ subject.credit }}</td>
                  <td>
                    <button class="btn btn-warning btn-sm mx-1" @click="openEditSubjectModal(subject)">แก้ไข</button>
                    <button class="btn btn-danger btn-sm" @click="deleteSubject(index)">ลบ</button>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>

      <!-- Edit User Modal -->
      <div class="modal fade" id="editUserModal" tabindex="-1">
        <div class="modal-dialog">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title">แก้ไขข้อมูลผู้ใช้</h5>
              <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
            </div>
            <div class="modal-body">
              <input type="text" class="form-control mb-2" v-model="dataUserEdit.id" placeholder="รหัสนิสิต">
              <input type="text" class="form-control mb-2" v-model="dataUserEdit.name" placeholder="ชื่อ">
              <input type="text" class="form-control mb-2" v-model="dataUserEdit.major_id" placeholder="รหัสสาขา">
              <input type="text" class="form-control mb-2" v-model="dataUserEdit.major_name" placeholder="สาขา">
              <input type="text" class="form-control mb-2" v-model="dataUserEdit.old_school" placeholder="โรงเรียนเดิม">
            </div>
            <div class="modal-footer">
              <button class="btn btn-secondary" data-bs-dismiss="modal">ปิด</button>
              <button class="btn btn-primary" @click="saveUserData" data-bs-dismiss="modal">บันทึก</button>
            </div>
          </div>
        </div>
      </div>

      <!-- Add/Edit Subject Modal -->
      <div class="modal fade" id="subjectModal" tabindex="-1">
        <div class="modal-dialog">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title">{{ editingSubject ? 'แก้ไข' : 'เพิ่ม' }} รายวิชา</h5>
              <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
            </div>
            <div class="modal-body">
              <input type="text" class="form-control mb-2" v-model="subjectForm.id" placeholder="รหัสวิชา">
              <input type="text" class="form-control mb-2" v-model="subjectForm.name" placeholder="ชื่อวิชา">
              <input type="text" class="form-control mb-2" v-model="subjectForm.grade" placeholder="เกรด">
              <input type="text" class="form-control mb-2" v-model="subjectForm.credit" placeholder="หน่วยกิต">
            </div>
            <div class="modal-footer">
              <button class="btn btn-secondary" data-bs-dismiss="modal">ปิด</button>
              <button class="btn btn-success" @click="saveSubject" data-bs-dismiss="modal">{{ editingSubject ? 'บันทึกการแก้ไข' : 'เพิ่ม' }}</button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>

  <script setup>
  import { ref, onMounted } from "vue";

  const dataUser = ref({
    id: "",
    name: "",
    major_id: "",
    major_name: "",
    old_school: "",
  });

  const dataUserEdit = ref({
    id: "",
    name: "",
    major_id: "",
    major_name: "",
    old_school: "",
  });

  const subjects = ref([]);
  const subjectForm = ref({});
  const editingSubject = ref(null);

  const openEditUserModal = () => {
    dataUserEdit.value = { ...dataUser.value }
    new bootstrap.Modal(document.getElementById("editUserModal")).show();
  };

  const saveUserData = async() => {
    await fetch('http://localhost:3000/users/', {
      method: 'PUT',
      headers: {'Content-Type': 'application/json'},
      body: JSON.stringify(dataUserEdit.value)
    })

    setUser();
  };

  const openAddSubjectModal = () => {
    subjectForm.value = { name: "", id: "" };
    editingSubject.value = null;
    new bootstrap.Modal(document.getElementById("subjectModal")).show();
  };

  const openEditSubjectModal = (subject) => {
    subjectForm.value = { ...subject };
    editingSubject.value = subject;
    new bootstrap.Modal(document.getElementById("subjectModal")).show();
  };

  const saveSubject = async() => {
    if (editingSubject.value) {
      if (subjectForm.value.id === editingSubject.value.id) {
        await fetch(`http://localhost:3000/subjects/${editingSubject.value.id}`, {
          method: 'PUT',
          headers: {'Content-Type': 'application/json'},
          body: JSON.stringify(subjectForm.value)
        })
      } else {
        await fetch(`http://localhost:3000/subjects/${editingSubject.value.id}`, {
          method: 'DELETE',
          headers: {'Content-Type': 'application/json'}
        })

        await fetch('http://localhost:3000/subjects/', {
          method: 'POST',
          headers: {'Content-Type': 'application/json'},
          body: JSON.stringify(subjectForm.value)
        })
      }

      setSubject();
    } else {
      if (subjectForm.value.id && subjectForm.value.name && subjectForm.value.credit && subjectForm.value.grade) {
        await fetch('http://localhost:3000/subjects/', {
          method: 'POST',
          headers: {'Content-Type': 'application/json'},
          body: JSON.stringify(subjectForm.value)
        })
        subjects.value.push({ ...subjectForm.value });
      }
    }
    new bootstrap.Modal(document.getElementById("subjectModal")).hide();
  };

  const deleteSubject = async(index) => {
    await fetch(`http://localhost:3000/subjects/${subjects.value[index].id}`, {
      method: 'DELETE',
      headers: {'Content-Type': 'application/json'}
    })

    subjects.value.splice(index, 1);
  };

  const setUser = async() => {
    const userRespone = await fetch('http://localhost:3000/users/');
    dataUser.value = await userRespone.json();
  }

  const setSubject = async() => {
    const subjectsRespone = await fetch('http://localhost:3000/subjects/');
    subjects.value = await subjectsRespone.json();
  }

  onMounted(async() => {
    setUser();
    setSubject();
  })
  </script>

  <style>
  @property --angle {
      syntax: "<angle>";
      initial-value: 0deg;
      inherits: false;
  }
  @keyframes spin {
      from {
          --angle: 0deg;
      }
      to {
          --angle: 360deg;
      }
  }
  .main-bg {
    background: linear-gradient(135deg, #ff9a9e, #fad0c4, #fad0c4, #ffdde1);
    min-height: 100vh;
  }

  .profile-img {
    width: 400px;
    height: 400px;
  }
  .menu-container {
    background: #f8d7da;
    padding: 20px;
    border-radius: 15px;
    box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.1);
  }
  .leftdetail{
    margin-left: 100px;
    margin-top: 20px;
    flex: 0 0 35%;
    max-width: 35%;
    text-align: center;
  }
  .ildetail{
    width: 400px;
    height: 400px;
    transform-style: preserve-3d;
    transition: all 1s ease;
    position: relative;
    transform-origin: center;
  }
  .ildetail:hover{
    transform: rotateY(180deg);
  }
  .card-body{
    width: 400px;
  }
  .card-title{
    color: rgb(77, 77, 77);
  }
  .front {
    position: absolute;
    width: 100%;
    height: 100%;
    backface-visibility: hidden;
    border-right: 1px solid rgba(255, 255, 255, 0.1);
    border-radius: 20px;
  }
  .back {
    position: absolute;
    width: 100%;
    height: 100%;
    backface-visibility: hidden;
    border-right: 1px solid rgba(255, 255, 255, 0.1);
    border-radius: 20px;
    transform: rotateY(180deg);
}
  </style>


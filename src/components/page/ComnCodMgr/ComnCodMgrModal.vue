<template>
    <teleport to="body">
        <div class="backdrop">
            <div class="container">
                <div class="header">그룹 코드 상세</div>
                <div v-if="isLoding">기다려보세요</div>
                <tbody v-else>
                    <tr>
                        <th>그룹 코드 id *</th>
                        <td>
                            <input
                                type="text"
                                name="grp_cod"
                                v-model="comnGrpCodObject.grp_cod"
                                :readonly="grpCodProp === 'create' ? false : true"
                            />
                            <input
                                type="text"
                                name="grp_cod"
                                v-model="comnGrpCodObject.grp_cod"
                                :readonly="grpCodProp === 'create' ? false : true"
                            />
                        </td>
                        <th>그룹 코드 명 *</th>
                        <td>
                            <input type="text" name="grp_cod_nm" v-model="comnGrpCodObject.grp_cod_nm" />
                            <input type="text" name="grp_cod_nm" v-model="comnGrpCodObject.grp_cod_nm" />
                        </td>
                    </tr>
                    <tr>
                        <th>코드 설명</th>
                        <td colSpan="3">
                            <input type="text" v-model="comnGrpCodObject.grp_cod_eplti" />
                            <input type="text" v-model="comnGrpCodObject.grp_cod_eplti" />
                        </td>
                    </tr>
                    <tr>
                        <th>사용 유무 *</th>
                        <td colspan="3">
                            <input type="radio" name="useYn" value="Y" v-model="comnGrpCodObject.use_poa" />
                            <input type="radio" name="useYn" value="Y" v-model="comnGrpCodObject.use_poa" />
                            사용
                            <input type="radio" name="useYn" value="N" v-model="comnGrpCodObject.use_poa" />
                            <input type="radio" name="useYn" value="N" v-model="comnGrpCodObject.use_poa" />
                            미사용
                        </td>
                    </tr>
                </tbody>
                <div class="btn-group">
                    <button @click="grpCodProp === 'create' ? insertComnCod() : updateComnCod()">
                        {{ grpCodProp === "create" ? "등록" : "수정" }}
                    </button>
                    <button v-if="grpCodProp !== 'create'" @click="deleteComnCod">삭제</button>
                    <button @click="modalState.setModalState">닫기</button>
                    <button @click="grpCodProp === 'create' ? insertComnCod() : updateComnCod()">
                        {{ grpCodProp === "create" ? "등록" : "수정" }}
                    </button>
                    <button v-if="grpCodProp !== 'create'" @click="deleteComnCod">삭제</button>
                    <button @click="modalState.setModalState">닫기</button>
                </div>
            </div>
        </div>
    </teleport>
</template>

<script setup>
import { useModalStore } from "@/stores/modalState";
import { useMutation, useQuery, useQueryClient } from "@tanstack/vue-query";
import axios from "axios";

const modalState = useModalStore();
const comnGrpCodObject = ref(new Object());
const queryClient = useQueryClient();
const { grpCodProp } = defineProps(["grpCodProp"]);

const comnCodInsert = async () => {
    await axios.post("/api/system/saveComnGrpCodJson.do", comnGrpCodObject.value);
};

const postSuccessFn = () => {
    modalState.setModalState();
    queryClient.invalidateQueries({
        queryKey: ["listComnGrp"]
    }); // 저장 후 재조회 하게하는 부분
};

const { mutate: insertComnCod } = useMutation({
    mutationFn: comnCodInsert,
    onSuccess: postSuccessFn
});

const searchDetail = async () => {
    const result = await axios.post("/api/system/selectComnGrpCod.do", { grp_cod: grpCodProp });
    return result.data;
};

const { isLoding } = useQuery({
    queryKey: ["detail", grpCodProp],
    queryFn: searchDetail,
    enabled: grpCodProp !== "create",
    staleTime: 60 * 1000,
    select: (data) => {
        comnGrpCodObject.value = data.comnGrpCodModel;
    }
});

const comnCodUpdate = async () => {
    await axios.post("/api/system/updateComnGrpCodJson.do", comnGrpCodObject.value);
};

const { mutate: updateComnCod } = useMutation({
    mutationFn: comnCodUpdate,
    onSuccess: postSuccessFn
});

const comnCodDelete = async () => {
    await axios.post("/api/system/deleteComnGrpCodJson.do", { grp_cod: grpCodProp });
};

const { mutate: deleteComnCod } = useMutation({
    mutationFn: comnCodDelete,
    onSuccess: postSuccessFn
});
</script>

<style lang="scss" scoped>
.backdrop {
    top: 0%;
    left: 0%;
    width: 100%;
    height: 100%;
    position: fixed;
    display: flex;
    flex-flow: row wrep;
    justify-content: center;
    align-items: center;
    background: rgba(0, 0, 0, 0.5);
    z-index: 1;
    font-weight: bold;
}
.header {
    background-color: #e0e0e0;
    padding: 10px 100% 10px 0px;
    border: 1px solid #ccc;
    box-sizing: border-box;
    display: flex;
    align-items: center;
    padding: 16px;
    font-size: 20px;
    margin-right: auto;
}
label {
    display: flex;
    flex-direction: column;
}

input[type="text"] {
    padding: 8px;
    margin-top: 5px;
    margin-bottom: 5px;
    border-radius: 4px;
    border: 1px solid #ccc;
}

.container {
    background: white;
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.25);
    position: relative;
    width: 600px;
    height: 300px;
    align-content: center;
}

.btn-group {
    text-align: right;
    margin-top: 10px;
    float: inline-end;
}

button {
    background-color: #3bb2ea;
    border: none;
    color: white;
    padding: 10px 22px;
    text-align: right;
    text-decoration: none;
    display: inline-block;
    font-size: 16px;
    margin: 4px 2px;
    cursor: pointer;
    border-radius: 12px;
    box-shadow: 0 4px #999;
    transition: 0.3s;

    &:hover {
        background-color: #45a049;
    }

    &:active {
        background-color: #3e8e41;
        box-shadow: 0 2px #666;
        transform: translateY(2px);
    }
}
</style>

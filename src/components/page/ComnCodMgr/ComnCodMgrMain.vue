<template>
    <button @click="handlerInsertModal">신규등록</button>
    <table>
        <colgroup>
            <col width="20%" />
            <col width="10%" />
            <col width="20%" />
            <col width="7%" />
            <col width="10%" />
            <col width="5%" />
        </colgroup>
        <thead>
            <tr>
                <th>그룹코드</th>
                <th>그룹코드명</th>
                <th>그룹코드 설명</th>
                <th>사용여부</th>
                <th>등록일</th>
                <th>비고</th>
            </tr>
        </thead>
        <tbody>
            <tr v-if="isLoading">
                <td colspan="6">로딩 중...</td>
            </tr>
            <template v-else-if="listComnGrp.listComnGrpCod.length <= 0">
                <tr>
                    <td colspan="6">데이터가 없습니다.</td>
                </tr>
            </template>
            <template v-else>
                <tr v-for="comnGrp in listComnGrp.listComnGrpCod" :key="comnGrp.grp_cod">
                    <td>{{ comnGrp.grp_cod }}</td>
                    <td>{{ comnGrp.grp_cod_nm }}</td>
                    <td>{{ comnGrp.grp_cod_eplti }}</td>
                    <td>{{ comnGrp.use_poa }}</td>
                    <td>{{ comnGrp.fst_enlm_dtt }}</td>
                    <td><a @click="(e) => handlerUpdateModal(e, comnGrp.grp_cod)">수정</a></td>
                </tr>
            </template>
        </tbody>
    </table>
    <Pagination
        :totalItems="listComnGrp?.totalCount || 0"
        :itemsPerPage="5"
        :maxPagesShown="5"
        :onClick="refetch"
        v-model="cPage"
    ></Pagination>
    현재 페이지: {{ cPage }} 총 개수: {{ listComnGrp?.totalCount }}
</template>

<script setup>
import { useQuery } from "@tanstack/vue-query";
import axios from "axios";
import ComnCodMgrModal from "./ComnCodMgrModal.vue";
import { useModalStore } from "@/stores/modalState";
import Pagination from "@/components/common/Pagination.vue";
import useGetComnGrp from "@/hook/comnCodMgr/useGetComnGrp";

const injectedValue = inject("providedValue");
const cPage = ref(1);
const pageSize = ref(5);
const modalState = useModalStore();
const grpCodProp = ref();

const searchList = async () => {
    const result = await axios.post("/api/system/listComnGrpCodJson.do", {
        currentPage: cPage.value,
        pageSize: pageSize.value,
        ...injectedValue.value
    });

    return result.data;
};
const {
    data: listComnGrp,
    isLoading,
    refetch
} = useQuery({
    queryKey: ["listComnGrp", injectedValue],
    queryFn: searchList
    // refetchInterval: 5 * 1000
});

const handlerInsertModal = () => {
    grpCodProp.value = "create";
    modalState.setModalState();
};

const handlerUpdateModal = (event, grpCod) => {
    grpCodProp.value = grpCod;
    modalState.setModalState();
};
</script>

<style lang="scss" scoped>
table {
    width: 100%;
    border-collapse: collapse;
    margin: 20px 0px 0px 0px;
    font-size: 18px;
    text-align: left;

    th,
    td {
        padding: 8px;
        text-align: left;
        border-bottom: 1px solid #ddd;
        text-align: center;
    }

    th {
        background-color: #2676bf;
        color: #ddd;
    }

    /* 테이블 올렸을 때 */
    tbody tr:hover {
        background-color: #d3d3d3;
        opacity: 0.9;
        cursor: pointer;
    }
}

a {
    cursor: pointer;
    &:hover {
        text-decoration: underline;
        color: white;
    }
}

button {
    text-align: center;
    text-decoration: none;
    display: block;
    border: none;
    color: white;
    width: 80px;
    padding-top: 10px;
    padding-bottom: 10px;
    font-size: 12px;
    margin: 10px 2px;
    cursor: pointer;
    border-radius: 12px;
    box-shadow: 0 4px #999;
    background-color: #3bb2ea;
    float: inline-end;

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

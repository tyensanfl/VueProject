<template>
    <div class="divComGrpCodList">
        <NoticeModal
            v-if="modalState.modalState"
            v-bind:notice-seq="noticeSeq"
            @modalClose="() => (noticeSeq = 0)"
            @postSuccess="searchList"
        ></NoticeModal>
        현재 페이지: {{ 0 }} 총 개수: {{ noticeList?.listCount }}
        <table>
            <colgroup>
                <col width="10%" />
                <col width="50%" />
                <col width="30%" />
                <col width="10%" />
            </colgroup>

            <thead>
                <tr>
                    <th scope="col">번호</th>
                    <th scope="col">제목</th>
                    <th scope="col">작성일</th>
                    <th scope="col">작성자</th>
                </tr>
            </thead>
            <tbody>
                <template v-if="noticeList">
                    <template v-if="noticeList.listCount > 0">
                        <tr
                            v-for="item in noticeList.noticeList"
                            v-bind:key="item.noti_seq"
                            @click="handlerDetail(item.noti_seq)"
                        >
                            <td>{{ item.noti_seq }}</td>
                            <td>{{ item.noti_title }}</td>
                            <td>{{ item.noti_date }}</td>
                            <td>{{ item.loginID }}</td>
                        </tr>
                    </template>
                    <template v-else>
                        <tr>
                            <td colspan="7">일치하는 검색 결과가 없습니다</td>
                        </tr>
                    </template>
                </template>
            </tbody>
        </table>
        <Pagination
            :totalItems="noticeList?.listCount"
            :itemsPerPage="5"
            :maxPagesShown="5"
            :onClick="searchList"
            v-model="cPage"
        />
        <Pagination
            :totalItems="noticeList?.listCount"
            :itemsPerPage="5"
            :maxPagesShown="5"
            :onClick="searchList"
            v-model="cPage"
        />
    </div>
</template>

<script setup>
import Pagination from "@/components/common/Pagination.vue";
import axios from "axios";
import NoticeModal from "./NoticeModal.vue";
import { useModalStore } from "@/stores/modalState";

const route = useRoute();
const noticeList = ref();
const cPage = ref(1);
const modalState = useModalStore();
const noticeSeq = ref(0);

const searchList = () => {
    let param = new URLSearchParams({
        cpage: cPage.value,
        pageSize: 5,
        searchTitle: route.query.searchTitle || "",
        searchStDate: route.query.searchStDate || "",
        searchEdDate: route.query.searchEdDate || ""
    });

    axios.post("/api/board/noticeListJson.do", param).then((res) => {
        noticeList.value = res.data;
    });
};

const handlerDetail = (seq) => {
    noticeSeq.value = seq;
    modalState.setModalState();
};

watch(route, searchList);

onMounted(() => {
    searchList();
});
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
</style>

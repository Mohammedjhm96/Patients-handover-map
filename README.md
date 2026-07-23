<div class="dept-card">
    <div class="dept-banner bg-neurosurgery">
        <span>ردهة الجراحة العصبية (Neurosurgery Ward)</span>
    </div>
    
    <table class="handover-table">
        <thead>
            <tr>
                <th style="width: 8%;">الحالة</th>
                <th style="width: 18%;">اسم المريض</th>
                <th style="width: 8%;">العمر</th>
                <!-- حقل GCS خاص بردهة الجراحة العصبية -->
                <th style="width: 18%;">مقياس الوعي (GCS)</th>
                <th style="width: 15%;">الطبيب الاختصاص</th>
                <th style="width: 15%;">التشخيص الطبي</th>
                <th style="width: 18%;">الخطة العلاجية والتفاصيل</th>
            </tr>
        </thead>
        <tbody>
            <!-- مثال لصف مريض -->
            <tr>
                <td class="cell-editable" contenteditable="true">Case 1</td>
                <td class="cell-editable" contenteditable="true">خالد مروان</td>
                <td class="cell-editable" contenteditable="true">45</td>
                
                <!-- خلية GCS التفاعلية -->
                <td>
                    <div class="gcs-box">
                        <div class="gcs-selects action-element">
                            <select onchange="updateGCS(this)" class="gcs-e" title="Eye Opening">
                                <option value="4">E4 (Spontaneous)</option>
                                <option value="3">E3 (To speech)</option>
                                <option value="2">E2 (To pain)</option>
                                <option value="1">E1 (None)</option>
                            </select>
                            <select onchange="updateGCS(this)" class="gcs-v" title="Verbal Response">
                                <option value="5">V5 (Oriented)</option>
                                <option value="4">V4 (Confused)</option>
                                <option value="3">V3 (Inappropriate)</option>
                                <option value="2">V2 (Incomprehensible)</option>
                                <option value="1">V1 (None)</option>
                            </select>
                            <select onchange="updateGCS(this)" class="gcs-m" title="Motor Response">
                                <option value="6">M6 (Obeys)</option>
                                <option value="5">M5 (Localizes)</option>
                                <option value="4">M4 (Withdrawal)</option>
                                <option value="3">M3 (Decorticate)</option>
                                <option value="2">M2 (Decerebrate)</option>
                                <option value="1">M1 (None)</option>
                            </select>
                        </div>
                        <div class="gcs-result">
                            <span class="gcs-score">15/15</span>
                            <small class="gcs-details">(E4 V5 M6)</small>
                        </div>
                    </div>
                </td>

                <td class="cell-editable" contenteditable="true">د. ممدوح صلاح</td>
                <td class="cell-editable" contenteditable="true">Extradural Hematoma</td>
                <td class="cell-editable" contenteditable="true">For craniotomy / Urgent CT scan</td>
            </tr>
        </tbody>
    </table>
</div>

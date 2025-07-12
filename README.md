function calculateSalary() {
  const baseSalary = parseFloat(document.getElementById('baseSalary').value);
  const workingDays = parseFloat(document.getElementById('workingDays').value);
  const shortHours = parseFloat(document.getElementById('shortHours').value);
  const overtimeHours = parseFloat(document.getElementById('overtimeHours').value);
  const tdsAmount = parseFloat(document.getElementById('tdsAmount').value) || 0;

  const applyPF = document.getElementById('pfCheckbox').checked;
  const applyESI = document.getElementById('esiCheckbox').checked;

  if (isNaN(baseSalary) || isNaN(workingDays) || isNaN(shortHours) || isNaN(overtimeHours) ||
      baseSalary <= 0 || workingDays <= 0 || shortHours < 0 || overtimeHours < 0 || tdsAmount < 0) {
    alert("Please enter valid non-negative values.");
    return;
  }

  localStorage.setItem('baseSalary', baseSalary);
  localStorage.setItem('workingDays', workingDays);
  localStorage.setItem('shortHours', shortHours);
  localStorage.setItem('overtimeHours', overtimeHours);
  localStorage.setItem('tdsAmount', tdsAmount);

  const perDay = baseSalary / workingDays;
  const perHour = perDay / 8;

  const shortHoursDeduction = shortHours * perHour;
  const overtimePay = overtimeHours * perHour;

  const lwfDeduction = 5;

  const earnedSalary = baseSalary - shortHoursDeduction;

  let pfDeduction = 0;
  if (applyPF) {
    pfDeduction = earnedSalary * 0.12; // PF on earned salary
  }

  let esiDeduction = 0;
  if (applyESI && baseSalary <= 21000) {
    esiDeduction = baseSalary * 0.0075;
  }

  const finalSalary = earnedSalary
    + overtimePay
    - lwfDeduction
    - pfDeduction
    - esiDeduction
    - tdsAmount;

  document.getElementById('result').style.display = 'block';
  document.getElementById('result').innerHTML = `
    <strong>Per Day:</strong> ₹${perDay.toFixed(2)}<br>
    <strong>Per Hour:</strong> ₹${perHour.toFixed(2)}<br>
    <strong>Short Hours Deduction:</strong> -₹${shortHoursDeduction.toFixed(2)}<br>
    <strong>Overtime Pay:</strong> +₹${overtimePay.toFixed(2)}<br>
    <strong>LWF Deduction:</strong> -₹${lwfDeduction.toFixed(2)}<br>
    ${applyPF ? `<strong>PF Deduction (12% on earned salary):</strong> -₹${pfDeduction.toFixed(2)}<br>` : ""}
    ${applyESI && baseSalary <= 21000 ? `<strong>ESI Deduction (0.75%):</strong> -₹${esiDeduction.toFixed(2)}<br>` : ""}
    ${tdsAmount > 0 ? `<strong>TDS Deduction:</strong> -₹${tdsAmount.toFixed(2)}<br>` : ""}
    <hr>
    <strong>Final Salary:</strong> ₹${finalSalary.toFixed(2)}
  `;
}
